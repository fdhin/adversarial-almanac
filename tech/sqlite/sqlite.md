---
technology: SQLite
kind: overview
audience: offensive-security
family: databases
layer: Embedded SQL engine (single-file database, in-process library, no daemon/network surface)
language: C
first-released: 2000
license: Public Domain
homepage: "https://www.sqlite.org"
github-repo: "sqlite/sqlite"
evidence: "43 writeups (command-context gate: sqlite3 CLI invocations on database files, sqlite> dot-commands, SQLite file-magic strings, load_extension/ATTACH payloads, SQLite-specific injection oracles, PDO/sqlite3 API lines, sqlitebrowser/DB Browser inspection, named-database exfil commands; the parent dispatch estimate was ~31 — the strict gate confirms all 7 named seeds except one bare-mention seed and adds sweep finds)"
status: enriched
provenance: "corpus-mined from 43 writeups in the writeup corpus (see ../internal coverage ledger) + upstream-verified: sqlite.org/lang_attach.html (ATTACH DATABASE semantics, sqlite3_open filename semantics), sqlite.org/c3ref/open.html (SQLITE_OPEN_READWRITE|SQLITE_OPEN_CREATE default create-on-open), SQLite source loadext.c + lang_corefunc/c3ref docs via python.org discussion quoting them (extension loading disabled by default, 'not authorized' error, SQLITE_DBCONFIG vs C-API split), accessed 2026-11-12"
---

# SQLite

> No daemon, no users, no network listener, no grants — the database is one file, "the credentials" live in the app's source, and any file-read primitive on the host is a full database dump.

## What it is

SQLite (2000, D. Richard Hipp and the SQLite team, public domain) is the embedded relational engine linked directly into the application process. There is no server to attack over the wire: the library opens a single file on disk, and every query is function calls inside the app's own process. On engagements it appears as the backend of small and mid-size web applications (often Python Flask/Django instances and PHP apps), and as the storage engine bundled inside larger products — code-hosting platforms, dashboards, database admin tools, monitoring and backup appliances keep their users, sessions, and configuration in a `.db`/`.sqlite`/`.sqlite3` file next to the application code.

The operator's mental model: **the file is the database and the filesystem is the security model**. Whatever process can read the file can read every table (and usually copy it away whole); whatever process can write its directory can replace it. There is no privilege system inside the file to slow you down once you have the file or SQL execution.

The takeaway: SQLite's attack surface is not a service — it is two primitives. First, SQL injection inside the hosting app, which runs with the app user's full filesystem reach and (unlike a network daemon) can write files and load native code through the engine. Second, file theft: exfiltrating one database file gives you the product's entire user/session/config store to mine offline with the `sqlite3` CLI. Injection chains and pure looting both end in the same place — credentials, sessions, and configuration rows in plaintext-readable tables.

## The anatomy that matters on an engagement

    request → app (Flask/PHP/Rails/Go/Node process)
      → sqlite library IN-PROCESS (no socket, no auth, no query log)
      → single file on disk (+ -wal / -shm sidecars when WAL mode is on)

    attack entry A: SQL string built in app code → injection (UNION / blind CASE WHEN / stacked statements)
      → sqlite_master enumeration → data rows
      → ATTACH DATABASE → CREATE TABLE + INSERT → FILE WRITE (webshell into the web root)
      → load_extension() → native library load → RCE (only if enabled at connection)
    attack entry B: any file-read/copy/LFI on the host or a writable share
      → copy *.db (+ WAL sidecars) out (scp / nc / /dev/tcp / SMB)
      → offline: file(1) magic, sqlite3 CLI, .dump, GUI browsers

Three structural properties drive most findings:

1. **The schema is self-describing and public.** `sqlite_master` holds every table's `CREATE` statement; injection or CLI access starts at one query — `SELECT name, sql FROM sqlite_master` — and there are no information-schema rights to fight.
2. **The engine can touch the filesystem.** `ATTACH DATABASE '/path/x' AS x` followed by `CREATE TABLE`/`INSERT` writes a new database file wherever the app user can write — the classic route from SQL injection to a PHP webshell in the web root. `load_extension()` can load a native shared object when the connection allows it. These are features, not bugs.
3. **Data at rest is plaintext to the row.** Application password hashes are stored inside the file as ordinary values, but everything else — session tokens, API keys, reset tokens, email addresses — is raw text, so even a partial file carve or a `strings` pass pays out.

## Where you'll meet it

- **Greenfield web apps** — Flask `instance/*.db`, Django `db.sqlite3`, Rails `db/development.sqlite3`, PHP `PDO('sqlite:...')`. Small teams ship the zero-config default to production; the development-mode SQLite file frequently survives into prod deploys and backups.
- **The bundled store inside bigger products** — self-hosted Git platforms, dashboards, monitoring and backup services, admin tools keep a `grafana.db`/`gitea.db`-style database under their data directory. You rarely attack these as SQLite; you attack the file permission that lets your current user read them, and the product's user/session tables do the rest.
- **Desktop and appliance state** — browsers (`places.sqlite`), Windows services and appliances keeping `*.db` state files, backup-server configuration databases. Foothold loot that turns into credential pivots.
- **Attacker-side, offline** — the `sqlite3` CLI is also the working end of evidence processing (converting a dumped NTDS database into a queryable SQLite file), so fluency with its dot-commands is table stakes regardless of the target's stack.

## Reading the engine's mind

- **Identify the backend from the injection itself.** `UNION SELECT sqlite_version(),2,3` answers with a 3.x version string; MySQL/PostgreSQL error out. Blind contexts: SQLite has no `SLEEP()` — boolean `CASE WHEN` and error oracles carry the enumeration instead.
- **The error oracle is `not authorized`.** Extension loading is disabled by default at the C level; calling `load_extension(1)` from SQL returns `not authorized` rather than a type error. That difference — error versus no error — is a clean boolean channel for blind injection, and it doubles as a probe: `load_extension('/dev/null')` answering `file too short` instead of `not authorized` means extension loading was left ENABLED on that connection (jump to the methodology's load-extension phase).
- **`ATTACH` creates files.** The attached filename follows `sqlite3_open()` semantics — read-write, created if missing — so an injected `ATTACH DATABASE '<webroot>/x.php' AS x; CREATE TABLE x.t(z); INSERT ...` writes a file whose body contains your row data after the `SQLite format 3` header; PHP and most interpreters ignore the leading binary bytes, which is what makes it a webshell pattern. It needs stacked statements (multi-statement support in the app's driver) and a writable target directory.
- **A whole database travels as one file (plus sidecars).** `file loot.db` printing `SQLite 3.x database` is your confirmation; always grab `-wal` and `-shm` siblings in WAL deployments, because the newest rows may live in the write-ahead log and not yet in the main file.
- **Offline reading needs no GUI.** The `sqlite3` CLI does everything the writeups reach for: `.tables`, `.schema <table>`, `.mode line`, `.headers on`, and `.dump` for a full SQL text export; GUI browsers (DB Browser for SQLite, sqlitebrowser) are the same operations with screenshots. If the database is corrupt or truncated, the CLI's `.recover` and plain `strings` still extract the plaintext rows.

## Where to go next

[[sqlite-bug-bounty|SQLite bug-bounty methodology]] — phased: confirm the backend → injection exploitation (UNION with sqlite_master, blind CASE WHEN, error oracles, ATTACH file-write, load_extension) → database-file loot and offline analysis → exfiltration patterns → source-review of sqlite-backed apps.
