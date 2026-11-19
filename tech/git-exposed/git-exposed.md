---
technology: "Exposed .git directories"
kind: overview
audience: offensive-security
family: web-platforms
layer: version-control metadata served by the web root (.git/ objects, index, refs, config) reconstructing application source and its secrets
first-released: n/a (attack surface, not software — applies to any git-backed deploy)
homepage: "https://git-scm.com"
evidence: "18 writeups (command-context gate: git-dumper/git_dumper.py invocations, HTTP fetches of /.git/HEAD|config|objects|index|logs, ffuf/dirb result lines for /.git paths, post-shell reads of .git/config for creds; the placeholder's 7 counted tools only — the dotgit-URL class triples it. Dropped: 11 files whose only git usage is ordinary git log/show on locally cloned or post-shell repos with no exposure)"
status: enriched
provenance: "corpus-mined from 18 writeups in the writeup corpus + upstream-verified: github.com/arthaud/git-dumper (pip-installable dumper, HEAD/index walk), github.com/internetwache/GitTools MIT (Finder/Dumper/Extractor split, pack-file limitation note), github.com/kost/dvcs-ripper (rip-git.pl -u http://target/.git/, also svn/hg/bzr/cvs), accessed 2026-11-12"
---

# Exposed .git directories

> The deploy copied the working tree but not the `.git` — so the web server is now shipping the project's entire history, every credential ever committed, and one file (`config`) that holds the origin URL and sometimes the push password.

## What it is

When a directory that is (or was) a git repository is served by a web server without removing `.git/`, that metadata directory becomes remotely readable: `HEAD` (current ref), `config` (remotes — and their credentials), `index` (binary staging manifest naming every file and blob hash), `objects/` (compressed blobs, trees, commits — sometimes packed), `logs/` (the reflog: the history *including* commits that were "removed"). Because the object store is content-addressed and self-describing, a dumper that walks `.git/HEAD → refs → commit → tree → blobs` reconstructs the whole working tree (and, with the logs, older trees) without directory listing being enabled — web servers happily serve the individual object files even when they 403 the directory index (observed: `/­.git/` 403 but `/.git/HEAD` 200, repeatedly).

The operator's mental model: **an exposed `.git` is not "source disclosure", it is the developer's laptop uploaded to the web root**. Three loot classes matter, in value order: **credentials in working files** — `.env`, `configuration.php`-style app configs, keys — as they exist in HEAD or in any historical commit; **the index vs the working-tree gap** — files staged but not committed (the dump's `git status` shows them; `git restore --staged . && git diff` reveals exactly what the developer was hiding when the snapshot leaked), and files present in `config`/history but deleted from HEAD (deleted-but-recoverable secrets are the classic finding); **`.git/config` itself** — remote origin URLs name internal git servers and subdomains, and legacy `user = X / password = Y` fields (or URLs with embedded credentials) hand over repo access. Submodule entries (`.gitmodules`, `gitlink` tree entries) extend the same recon to other repositories.

Second takeaway: the *finding* is routine — a directory brute-force hit on `/.git/HEAD` — but the value is entirely in the **commit archaeology** afterwards. The field's kills came from `git log` → `git show` of a "Security Fixes" commit (revealing a downgraded dependency), staged-but-uncommitted credentials, and a `config` password reused on SSH — not from the dump mechanics.

## The anatomy that matters on an engagement

    discover: ffuf/dirb/gobuster hit /.git/HEAD (200) — or nginx alias off-by-slash exposes it at /assets../.git/
      → dump: git-dumper URL outdir   (or GitTools gitdumper.sh; dvcs-ripper rip-git.pl)
      → reconstruct: git checkout . (dumper does it) → working tree as deployed
      → archaeology: git status (staged-not-committed!) → git log --all → git show/diff per commit
                     → deleted files, reverted "fixes", old credentials
      → loot: .git/config (remotes + creds), .env/config files, submodule refs → next repo
      → pivot: creds → app/DB/SSH; source → the actual app bug (the dump's real payload)

Three structural properties drive most findings:

1. **Object files are served even when the directory is not.** `/.git/` returns 403 or 301 while `/.git/HEAD`, `/.git/config`, `/.git/objects/xx/yyyy...` all return 200 (five observed engagements). Never stop at the 403.
2. **The index leaks filenames even when blobs are packed or missing.** `git-dumper` reads the binary `.git/index`; GitTools' Extractor recovers commit contents from broken dumps (`git cat-file` walk). Pack files (`objects/pack/`) are the dumper's known blind spot (upstream README) — request `objects/info/packs` and fetch pack files manually if history looks thin.
3. **History is the point.** Reflog (`logs/HEAD`), all refs, and dangling objects hold deleted secrets and reverted changes that the deployed app no longer shows (observed: one engagement's "Security Fixes" commit revealed the pre-fix vulnerable version; another's staged diff carried the real credentials).

## Where you'll meet it

- **`/dev`, `/staging`, `/backup` subdirectories of the main site** — developer copies of the app left beside production (observed: one `/dev/.git`; two more exposed theirs on dev-style and shop-style subdomains) — directory-fuzz every discovered path, not just the root.
- **nginx `alias` misconfigurations** — an `location /assets { alias /path/assets; }` off-by-slash exposes the web *root* via `/assets../.git/` (observed: one engagement dumped `.git` through exactly this traversal, and `.env` the same way).
- **Post-shell on any web box** — `cat /var/www/app/.git/config` is a two-second privesc attempt: origin URL (internal git server + subdomain) and sometimes inline credentials (observed: one chain's config creds + gitea subdomain reference led to root).
- **Distributed source packages** — "download our stack" zips that include `.git` (observed: one Docker bundle) or backup archives containing it (field-observed) — same archaeology, offline.
- **Adjacent VCS metadata** — `.svn/`, `.hg/` leak the same way (dvcs-ripper exists precisely for svn/hg/bzr/cvs); a miss on `.git` should prompt a try of the siblings.

## Reading the framework's mind

1. **A single 200 on `/.git/HEAD` is the whole gate** — content `ref: refs/heads/<branch>` (GitTools' Finder greps exactly this) means the dump will run; 403/404 on the directory itself changes nothing.
2. **The dumper's output tells you its own confidence** — `[-] Testing http://example.com/.git/HEAD [200]`, `Fetching ... [200]` per object, then `Running git checkout .` — a short fetch list means packed objects or partial exposure; go manual for `objects/info/packs`.
3. **Run `git status` before admiring the tree** — "Changes to be committed" on a dumped repo means the server's index disagrees with HEAD: those staged hunks are the freshest developer secrets (field-observed) and `git restore --staged . && git diff` prints them.
4. **`.git/config` is two findings in five lines** — the `url =` under `[remote "origin"]` names an internal host (often a git server unit in this vault: gitea/gitlab) and the legacy `insteadOf`/credential lines may carry the push password.
5. **Version-gate your conclusions** — source ≠ deployed version: the dump is a snapshot of the *repo*, and one observed admin spoofed a version file specifically to poison exactly this kind of recon (see the wordpress unit's version-file-spoofing lesson). Cross-check the dumped app version against runtime behavior.

## Where to go next

[[git-exposed-bug-bounty|Exposed .git bug bounty methodology]] — discovery markers, dumping workflows per tool, reconstruction and commit-archaeology commands, loot classes (staged secrets, config credentials, submodules), the write side (manual object walks through file primitives, hook injection for execution-on-commit), and disclosure framing.
