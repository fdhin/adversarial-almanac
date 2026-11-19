---
technology: "Subversion (SVN)"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
family: network-services
evidence: "1 file (see ../internal coverage ledger) — thin unit per standing rule; the evidence base supplies one executed chain (anonymous checkout → revision mining → on-disk repo loot), upstream (svnbook, Apache httpd docs, Subversion 1.7 release notes) supplies the rest"
provenance: "mined from 1 evidence file + upstream-verified: svnbook svnserve reference (default port 3690, -r/--root, conf/svnserve.conf defaults anon-access=read/auth-access=write, password-db), svnbook mod_dav_svn reference (SVNParentPath, SVNListParentPath default Off, SVNPathAuthz), svnbook repository hooks reference (post-commit REPOS REV; pre-revprop-change default-deny), Subversion 1.7 release notes (single .svn/wc.db), SANS .svn/wc.db pristine-reconstruction technique. Accessed 2026-11-12"
---

Companion page: [[svn|SVN overview]] — what the service is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# SVN Bug Bounty Methodology

**Scope:** the Subversion serving surface — svnserve on 3690 (`svn://`), Apache mod_dav_svn (`http(s)://…/svn`), working-copy metadata exposure (`.svn/`, `wc.db`) on web roots, and repository internals (conf/passwd, authz, hooks) once on disk. **Out of scope:** Git services (git-exposed unit), generic Apache (http-proxy unit), the CI systems repositories feed (the observed chain continued into Azure DevOps — that is the jenkins/devops surface, not SVN's).

**Authorization:** presume written permission. Checkout/log phases are read-only and low-impact; hook modification and commits touch shared developer infrastructure — never commit to a production repo, and treat `svn update -r` working copies as disposable.

## Decision tree (one screen)

1. `3690/tcp open svnserve` or an `<Location /svn>` vhost → Phase 1 (protocol probe).
2. Anonymous read works → Phase 2 (checkout + `svn log` revision mining).
3. Auth required → Phase 3 (passwd-file guesses, credential reuse from other loot).
4. Web server + suspected deployed checkout → Phase 5 (`.svn/wc.db` fetch chain).
5. On-host filesystem access → Phase 6 (repo conf/passwd, authz, hooks).

## Phase 1 — Service probe

```bash
nmap -p 3690 -sV 198.51.100.10                         # service: svnserve Subversion
(printf ''; sleep 2) | nc 198.51.100.10 3690           # raw protocol banner
svn ls svn://198.51.100.10/                            # anonymous read test (default anon-access=read)
svn ls http://example.com/svn/                         # Apache + SVNListParentPath On → repo index
curl -s http://example.com/.svn/entries                # pre-1.7 working copy probe
curl -s -o wc.db http://example.com/.svn/wc.db         # 1.7+ probe (WC-NG)
```

svnserve banners leak version on connect (grab with nc: `( success ( 2 ( ) ... )` protocol line). Apache form: `SVNParentPath` locations guess repo names (`/svn/<project>`); `SVNListParentPath On` (upstream default Off) lists them for you — also try `GET /svn/!svn/vcc/default` style requests to confirm DAV svn behavior without names.

## Phase 2 — Anonymous checkout + revision mining (observed chain)

```bash
svn checkout svn://198.51.100.10/ repo        # full tree + history, unauthenticated
cd repo && svn log                            # commit messages: rotation events, migrations, TODOs
svn log -v                                    # verbose: which paths changed per revision
svn diff -r 3:2                               # what changed between revisions
svn update -r 2                               # roll the working copy back to older creds (the observed chain did exactly this)
svn cat -r 1 conf/production.ini              # read any old file version without checkout surgery
```

Observed evidence: anonymous `svn checkout` on 3690 → `svn log` showed interesting early revisions → `svn update -r 2` recovered credentials committed then and changed later. Rules: never trust HEAD to be the interesting revision; diff old vs new for config/credential paths; commit messages name systems (migration notes in the evidence pointed at the next target's FQDN).

## Phase 3 — Authentication attacks (when anon-access=none)

- `password-db` files are `[users] name = password` plaintext; guesses follow the project's own vocabulary (names in `svn log` output from Phase 2 if you have read-only, repo README, commit authors). Reuse credentials harvested elsewhere on the box.
- SASL-enabled svnserve (`use-sasl = true`) fronts system auth — different loot path, same reuse rule.
- Apache-fronted repos: HTTP Basic against `/svn/<repo>` — hydra-class rate-limited testing where the engagement allows.

## Phase 4 — Apache mod_dav_svn enumeration

- Directory browsing: navigate `GET /svn/<repo>/` — mod_dav_svn serves basic listings (upstream: "limited repository browsing experience" — no revision properties, but files and dirs visible).
- `SVNIndexXSLT` styling confirms the module; `SVNReposName` shows in listing titles.
- Authorization is layered: httpd auth + optional `mod_authz_svn` (`AuthzSVNAccessFile`) + `SVNPathAuthz` subrequests — different URLs can sit behind different rules; probe `/svn/<repo>/trunk` vs `/svn/<repo>/conf` separately.

## Phase 5 — Web-exposed .svn/ (the .git-class finding)

Working copy deployed instead of `svn export` → metadata fetch chain (SANS-documented, upstream WC-NG format):

```bash
curl -s -o wc.db http://example.com/.svn/wc.db
sqlite3 wc.db 'select local_relpath, ".svn/pristine/" || substr(checksum,7,2) || "/" || substr(checksum,7) || ".svn-base" from NODES;'
curl -s -o file.svn-base "http://example.com/.svn/pristine/<2hex>/<sha1>.svn-base"   # per file
```

`REPOSITORY` table gives the source repo URL/UUID (pivot to Phase 1 against it); `NODES` gives the full tree; pristine blobs are the unmodified base files (pre-local-edits source). Report parity with an exposed `.git/`: full source disclosure, credential mining into every config file in tree.

## Phase 6 — On-disk repository loot (post-foothold)

```bash
cat /srv/svn/repos/<repo>/conf/svnserve.conf   # anon-access? password-db? realm?
cat /srv/svn/repos/<repo>/conf/passwd          # [users] plaintext passwords
cat /srv/svn/repos/<repo>/conf/authz           # path-based ACLs (who can read /trunk/secrets)
ls -la /srv/svn/repos/<repo>/hooks/            # post-commit, pre-commit, pre-revprop-change
```

Observed form: Windows host, repo root `w:\svnrepos\`, `www\conf\passwd` held usernames + hashes used for the next hop. Hooks: `post-commit REPOS-PATH REV` executes as the svnserve/httpd user — write access to `hooks/` is code execution as that user; a missing `pre-revprop-change` means revision metadata edits are blocked (default-deny), which is your gate for history-rewrite findings. `svnadmin` on disk can dump full repo history (`svnadmin dump <repo>`) — the complete secret archive in one file.

## Disclosure notes

- Anonymous checkout = full source + history disclosure: rate critical when the tree contains credentials (state which), medium otherwise.
- `.svn/wc.db` exposure severity matches `.git/` exposure on the same host — consistent rating.
- Repo `conf/passwd` plaintext: report file path + user count, not the passwords themselves.
- History-mined credentials: name the revision so the fix (rotation, not deletion — old revisions keep it forever without a dump/filter cycle) is actionable.

## Appendix A — Tool mapping (prose)

- **svn client** (`ls`, `checkout`, `log -v`, `diff`, `update -r`, `cat -r`, `export`) — the entire network surface; log/diff/cat are the quiet history miners.
- **nmap / nc** — 3690 service detection, raw banner grab.
- **curl / wget** — `.svn/wc.db` and pristine blob fetches, Apache `/svn/` probing.
- **sqlite3** — `wc.db` REPOSITORY/NODES/PRISTINE queries.
- **svnadmin** — on-disk repo dumps (post-foothold complete-history loot).
- **hydra** — HTTP Basic testing against Apache-fronted repos (rate-limit-aware).
