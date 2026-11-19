---
technology: "Subversion (SVN)"
kind: overview
audience: offensive-security
family: network-services
layer: "version-control service: svnserve daemon (3690), Apache mod_dav_svn, working-copy metadata (.svn/wc.db), repository hooks"
language: C
first-released: 2000
license: "Apache License 2.0"
homepage: "https://subversion.apache.org"
github-repo: "apache/subversion"
evidence: "1 file after strict command-context gate (placeholder claimed 4; collapses: an FTP directory named subversion-1.10.0, no svn service attacked; GitLab UI doc text 'To import an SVN repository'; 0x0369... V8 hex addresses). Thin unit — upstream supplies the surface, the evidence base supplies the one executed chain"
status: thin-enriched
provenance: "mined from 1 evidence file (see ../internal coverage ledger) + upstream-verified: svnbook (svnserve.conf anon-access/auth-access/password-db defaults, mod_dav_svn directives incl. SVNListParentPath default Off, repository hook reference post-commit/pre-revprop-change), Subversion 1.7 release notes (WC-NG single .svn/wc.db), SANS pen-test article on .svn/wc.db pristine reconstruction. Accessed 2026-11-12"
---

# Subversion (SVN)

> A central version-control server whose daemon ships with anonymous read ON by default — and whose client leaves a SQLite database (`.svn/wc.db`) in every deployment directory that names the source repo and points at pristine copies of every file. Old revisions remember deleted passwords.

## What it is

Subversion (2000, CollabNet → Apache Software Foundation project) is the centralized version-control system that predates Git in most enterprises and never fully left. Three serving modes matter to an operator: **svnserve**, the standalone daemon on TCP 3690 speaking the custom `svn://` protocol; **Apache httpd + mod_dav_svn**, serving repositories over WebDAV at `http(s)://` URLs; and `svn+ssh://` tunneling. Repositories on disk are directories containing `conf/` (`svnserve.conf`, `passwd`, `authz`), `db/` (revision store), and `hooks/` (executable scripts run by the server on commit events).

The operator's mental model: **a credential and secret archive that happens to be a server**. `svnserve.conf`'s documented default is `anon-access = read` — an unauthenticated stranger can check the whole repository out unless an admin explicitly wrote `none`. The password file is plaintext `[users] user = password` lines. The history is the payload: every revision of every config, key, and script the developers ever committed, including the ones they "removed" in a later commit. And the client side mirrors the `.git/` problem: since 1.7 a checked-out working copy keeps one `.svn/` directory at its root holding `wc.db` (SQLite: repository URL, UUID, file tree, checksums) plus `pristine/` — uncompressed base copies of every file.

The takeaway: SVN surfaces are quiet, unmonitored, and dense with secrets. On the network it is a 3690 banner away from a full source download; on a web root, a readable `.svn/` is the same class of finding as an exposed `.git/`; on a filesystem, repo `conf/passwd` and hooks are direct loot.

## The anatomy that matters on an engagement

    3690/tcp svnserve ──(anon-access=read default)──> svn checkout svn://host/ → full source + history
    http(s):// Apache + mod_dav_svn ──(SVNParentPath, maybe SVNListParentPath On)──> repo listing / browsing
    working copy on web root ──> .svn/wc.db download ──> NODES tree + pristine/XX/<sha1>.svn-base fetch → source
    repo on disk ──> conf/passwd (plaintext) ──> hooks/ (write = exec as daemon user)
    misbehavior lives at:
      1. default anonymous read
      2. revision mining (creds in r-1 that were rotated in rHEAD)
      3. .svn/ metadata exposure (same class as .git/)
      4. hook scripts as an execution primitive

Three structural properties drive most findings:

1. **Anonymous read is the default posture.** Upstream svnbook documents `# anon-access = read` as the sample default; most boxes inherit it. One unauthenticated `svn checkout` equals full source + full history disclosure.
2. **History is immutable loot.** `svn log` + `svn update -r <n>` walks time backwards; the observed chain recovered credentials committed in r2 that were scrubbed later. Deleted ≠ gone.
3. **The client leaks the server.** `.svn/wc.db` stores the repository root URL, UUID, and per-file SHA1 checksums mapping to `pristine/` blobs — fetchable one HTTP GET each over a web-exposed checkout.

## Where you'll meet it

- **Legacy enterprise dev boxes** — svnserve on 3690 next to ancient build infrastructure; rarely in anyone's monitoring scope.
- **Windows DevOps/IIS hosts** — observed chain: IIS + WinRM + svnserve on one box, repository root at `w:\svnrepos\` with `conf/passwd` on disk; the repo led to an Azure DevOps pipeline continuation.
- **Web-deployed working copies** — sites checked out (not exported) onto web roots; `.svn/wc.db` fetchable over HTTP exactly like `.git/config`.
- **Apache `svn.` vhosts** — `<Location /svn>` with `DAV svn` + `SVNParentPath`; `SVNListParentPath On` (default Off) turns `GET /svn/` into a repository index. Even with it Off, known repo names remain directly addressable.

## Reading the framework's mind

- `svnserve` reads `conf/svnserve.conf` **per repository** (unless `--config-file` overrides); `password-db = passwd` points at the plaintext `[users]` file relative to the conf dir. `realm` ties repos to shared password files — same realm often means same credentials across repos.
- `svn export` produces a clean tree with no `.svn/`; `svn checkout` leaves the metadata. Which one the deploy script used is the difference between a source-disclosure finding and nothing.
- Hooks: `post-commit REPOS-PATH REV` runs as the svnserve/httpd user after every commit; `pre-revprop-change` must exist and exit 0 or revision-property edits (history rewrites, `svn log` metadata) are refused — its absence is a hard default-deny.
- Working copies ≥1.7 keep ONE `.svn/` at the root (WC-NG): `wc.db` tables `REPOSITORY` (root URL, UUID), `NODES` (local paths, checksums), `PRISTINE` — every blob at `.svn/pristine/<first2-hex>/<sha1>.svn-base`.
- `svn cat -r <n> <path>`, `svn diff -r A:B`, and `svn log -v` read history without touching the working tree — the quietest way to mine old secrets once authenticated.

## Where to go next

[[svn-bug-bounty|SVN bug-bounty methodology]] — phased: port 3690 probe → anonymous checkout → revision mining → Apache svn vhost enumeration → .svn/wc.db web reconstruction → on-disk repo loot and hooks → tool mapping.
