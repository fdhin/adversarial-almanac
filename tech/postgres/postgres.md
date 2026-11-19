---
technology: PostgreSQL
kind: overview
audience: offensive-security
family: databases
layer: relational database server (SQL, client/server over TCP 5432 or Unix socket)
language: C
first-released: 1996
license: PostgreSQL License (BSD-style)
homepage: "https://www.postgresql.org"
github-repo: "postgres/postgres"
evidence: "14 writeups (command-context gate: psql invocations, 5432/tcp nmap lines, postgres:// connstrings, pg artifacts — pg_hba.conf, FROM PROGRAM, \\du, lo_import, pg_dump; re-gated 2026-11-12 from 25-file hint, bare-word 'postgres' excluded)"
status: enriched
provenance: "corpus-mined from 14 writeups in the writeup corpus + upstream-verified: postgresql.org docs (sql-copy, auth-pg-hba-conf, release-11, sql-createextension), postgresql.org/support/security/CVE-2023-39417 and CVE-2024-10979, accessed 2026-11-12"
---

# PostgreSQL

> A server that will execute shell commands (`COPY … FROM PROGRAM`), read and write arbitrary server files, and run extension scripts as the bootstrap superuser — every one of those gated only by role membership, which is why the first question after any foothold is "what does `\du` say?".

## What it is

PostgreSQL (PostgreSQL Global Development Group, first release 1996, C, permissive BSD-style license) is a relational database server. Clients — the `psql` terminal client, application drivers, or an SSRF pivot speaking the wire protocol — connect over TCP/5432 or a Unix-domain socket (`/var/run/postgresql/.s.PGSQL.5432`, also addressable as `-h /tmp` when the socket lives there), authenticate against `pg_hba.conf`, then speak SQL extended with psql meta-commands (`\du`, `\dt`, `\i`, `\copy`).

The operator's mental model: one trusted server process (running as the `postgres` OS user) that by design can touch the whole host — server-side `COPY` reads and writes files *as the server user*, `COPY … FROM PROGRAM` shells out, large objects (`lo_import`/`lo_export`) move arbitrary files through the database, and extension scripts run SQL as the installing role. The privilege gates exist (superuser, the PG 11 default roles `pg_read_server_files` / `pg_write_server_files` / `pg_execute_server_program`), but in the field they are almost always open: app accounts run as `postgres`, or SQL injection lands in a superuser session.

The takeaway: on the observed evidence, PostgreSQL is less often a network-facing front door (5432 was internet-exposed in only a handful of engagements) and more a **post-foothold pivot and privilege-escalation engine** — credentials harvested from application config are replayed at `psql`, and an over-privileged role converts directly to code execution on the DB host.

## The anatomy that matters on an engagement

    creds harvested (app config / env / bash history / connstring in source)
      → psql (TCP 5432 or Unix socket; localhost-only is the norm, tunnel it — chisel in the field)
      → pg_hba.conf check (first matching record wins; method trust/peer/md5/scram)
      → role enum: \du  (superuser? pg_read_server_files? CREATE on database?)
      → execution ladder:
           COPY tbl FROM PROGRAM 'cmd'        — needs superuser / pg_execute_server_program
           COPY tbl TO '/path'                — file write as server user
           lo_import/lo_export                — arbitrary file read/write via large objects
           postgresql.conf overwrite + pg_reload_conf()   — archive_command persistence/RCE
           CREATE EXTENSION (trusted)         — script SQL as bootstrap superuser

Three structural properties drive most findings:

1. **Server-side COPY is host file/command access by design.** "COPY naming a file or command is only allowed to database superusers or users who are granted one of the roles `pg_read_server_files`, `pg_write_server_files`, or `pg_execute_server_program`" (postgresql.org, sql-copy). It executed in 5 of the 14 observed engagements — more than any other primitive.
2. **Stacked queries + dollar-quoting make SQL injection equal to RCE** when the session is privileged: `…; COPY output FROM PROGRAM $$touch /tmp/proof$$;--` (an observed pattern). PostgreSQL also tolerates `UNION SELECT version()` as a clean injection oracle (field-observed).
3. **Credentials concentrate in application config** — `application.properties` (Spring), systemd `Environment=DATABASE_URL=postgresql://…`, Python `create_engine('postgresql://user:pass@localhost/db')`, bash history — and `pg_hba.conf`'s `trust`/`peer` rows or an unchanged `postgres` password mean those creds land in a superuser shell (4 observed engagements).

## Where you'll meet it

- **Backend of a web app, localhost-only** — the default shape in the field. You meet it after file read, LFI, or command execution: harvest the app's DB credentials, then `psql -h 127.0.0.1 -U <role>` for structured loot (password tables) and host RCE.
- **Dockerized / internal-network instances** — reached through tunnels (chisel reverse port-forward to a container's 5432 in the field) or from another compromised container; `docker ps` output showing `postgres:16` with `5432/tcp` is a map marker.
- **Rarely, directly exposed 5432** — when it happens, version fingerprinting (`SELECT version()`, banner, error messages) plus credential reuse against `postgres` or app roles is the whole game; SQLi in the front-end app is the alternative road in.
- **Windows hosts** — same engine, different escalation: `psql.exe` + `lo_import`/`lo_export` to write a DLL into System32 (field-observed), instead of Unix file tricks.

## Reading the framework's mind

- `pg_hba.conf` is **first-match**: the first record matching (type, address, database, user) sets the auth method; `local … peer` makes socket connections password-free for the matching OS user — which is exactly why `sudo -u postgres psql` (or `psql -h /tmp -U postgres` from a shell as the right user) skips authentication.
- Superuser checks for file/program access were **converted to grantable roles in PG 11** (`pg_read_server_files`, `pg_write_server_files`, `pg_execute_server_program`) — `\du` output listing those roles under a non-superuser is the whole RCE justification (field-observed).
- `\copy` is client-side; `COPY` is server-side. A locked-down `\copy` tells you nothing about server file rights — and vice versa.
- **Trusted extensions** (PG 11+ control file flag): `CREATE EXTENSION` for a trusted extension needs only `CREATE` privilege on the current database, not superuser — but the script still runs as the bootstrap superuser, which is the entire attack (see [[postgres-bug-bounty|methodology]], CVE-2023-39417).
- Version mismatches leak in banners: `psql (18.1 …, server 14.19 …)` style lines (field-observed) fingerprint the server precisely; `SELECT version()` does the same over SQLi.

## Where to go next

[[postgres-bug-bounty|PostgreSQL bug-bounty methodology]] — phased: fingerprint → credential replay → `\du`/`\dt` enum → the execution ladder (COPY FROM PROGRAM, large objects, conf overwrite, trusted extensions) → verified CVE table → tool mapping.
