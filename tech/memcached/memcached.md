---
technology: "Memcached"
kind: overview
audience: offensive-security
family: databases
layer: cache / key-value store
language: C
homepage: https://www.memcached.org
github-repo: https://github.com/memcached/memcached
provenance: "corpus-grounded (7 writeups) + upstream-verified (memcached docs, CVE records)"
---

# Memcached

> An unauthenticated, plaintext, line-based key-value cache that historically bound to **every interface** by default — the daemon's own man page admits binding is "the only way to secure the installation," and `stats cachedump` hands strangers the keys.

## What it is

Memcached is an in-memory hash of string keys → arbitrary blobs, spoken over a dead-simple ASCII protocol on **11211/tcp** (`get <key>`, `set <key> <flags> <ttl> <bytes>`, `stats`, terminated by `\r\n`). There is no query language, no auth in the base configuration (SASL is a compile- and opt-in feature tied to the binary protocol), and no per-key ACLs — whoever reaches the port owns the cache. It exists to make web apps fast, which means it sits one hop from the application holding exactly the data the app was too slow to recompute: session blobs, rendered fragments, API responses, password-reset tokens, and — far too often — passwords and internal hostnames that someone "temporarily" cached.

For the attacker, memcached matters in three distinct postures. **Direct**: an exposed 11211 answers `version` and `stats` to anyone, and `stats cachedump` walks slab classes to enumerate every key, then `get` reads them — full cache disclosure with zero exploits. **Pivot**: because the protocol is plaintext CRLF text, any SSRF that speaks raw bytes (gopher:// is the classic carrier) can write cache entries *through* the vulnerable web app — cache poisoning, session fixation, and deserialization traps. **Adjacent**: memcached is a thing apps trust blindly, so config around it (`memcached.ini`, supervisord program entries, `MEMCACHED_HOST` env vars) is host-attack surface in its own right.

## The anatomy that matters on an engagement

```
web app ──ascii/binary──► memcached :11211   (★ no auth; INADDR_ANY default on old builds)
   ▲                          │
   │ set/get keys              ├─ slabs: fixed-size chunk classes (stats slabs → stats items → cachedump)
   │                          └─ UDP 11211 (pre-1.5.6 default ON → 1:50,000 amplification)
   ▼
SSRF in the app ──gopher://host:11211/_%0d%0a<raw commands>──► (★ write keys as the app user would)
```

Structural properties that drive findings:

1. **No authentication by design in the ASCII protocol** — reachability is authorization. `-S` (SASL) is opt-in and forces the binary protocol; if you get a `STAT ...` reply, you own it.
2. **The stats family is a free metadata disclosure** — `stats`, `stats items`, `stats slabs`, `stats cachedump <class> <count>`, plus `stats maps`/`stats malloc` on ancient versions (CVE-2009-1255 leaked `/proc/self/maps` and ASLR layout).
3. **Text protocol = SSRF-pivotable** — `set` and `get` are CRLF-delimited lines, so a gopher URL with `%0d%0a` encodes a full command stream; apps that let you influence their cache backend requests become write primitives.

## Where you'll meet it

- **Internal-only 11211 found from a foothold** — the highest-yield case: netcat in, dump keys, read cached passwords/session tokens and pivot accounts (the classic cache-holds-a-password finding).
- **SSRF-capable PHP/Python apps with a memcache backend** — feed caches through the app with gopher; poison serialized objects the app later unserializes (PHP object injection via a cached `O:14:"..."` blob).
- **Legacy internet-facing instances** — pre-2018 boxes with UDP 11211 open are both walk-in caches and 50,000× DRDoS amplifiers (CVE-2018-1000115).
- **Shared/managed hosting control panels** — supervisord-style `memcached.ini` program configs; owning the directory the ini lives in lets you replace the program definition and ride the next config reload to execution.

## Reading the daemon's mind

- Default TCP port 11211; UDP off by default only since 1.5.6 (2018) — earlier 1.4.x speaks UDP unless `-U 0`.
- Default bind is INADDR_ANY unless `-l` narrows it — there is no other access control.
- `-X`/`--disable-dumping` exists precisely to kill `stats cachedump` and `lru_crawler metadump` — its presence in `ps` output tells you the operator knew.
- `-B binary` or clients setting `OPT_BINARY_PROTOCOL` mean SASL may be in play (auth errors on plain ASCII probes); `-S` in the process line confirms.
- Version probe: `version` command or `STAT version` from `stats` — gate the CVE table on it (below, in the methodology file).

## Where to go next

- [[memcached-bug-bounty]] — full method: banner/version → stats-family enumeration and key dumping → libmemcached tooling → gopher SSRF pivots and cache poisoning → version-gated CVE table.
