---
technology: "Squid"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
family: network-services
evidence: "5 files (see ../internal coverage ledger)"
provenance: "mined from 5 evidence files + upstream-verified 2026-11-12: squid-cache.org wiki Features/CacheManager + CacheManager/Index (/squid-internal-mgr well-known path, Basic-auth password check against cachemgr_passwd with username ignored, cache_object:// scheme until Squid 6.5), squid-cache.org/Doc/config/cachemgr_passwd (syntax, action list, disable/none/all keywords), wiki SquidClientTool page (curl -u against the manager). Supplement (2026-11-13) rewritten from HackTricks (src/network-services-pentesting/3128-pentesting-squid.md), CC BY-NC 4.0, accessed 2026-11-13 (egress validation, metadata/IPv6/s_client pivoting, spose + mysquidscanner verified against their repositories, megamansec audit verified at source, extra manager actions)."
---

Companion page: [[squid|Squid overview]] — what the technology is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# Squid Bug Bounty Methodology

**Scope:** attacking a Squid (or generic HTTP forward) proxy — enum through it, auth bypass, cache-manager abuse, WPAD/PAC discovery — and the SSRF-via-open-proxy pattern. **Out of scope:** reverse proxies and WAFs (web-platform units), attacker-side proxychains pivoting after a foothold (tooling, not target surface).

**Authorization:** presume written permission. Requests through a corporate proxy land on production internal systems; cache-manager admin actions (`shutdown`, `reconfigure`) are service-killing — never fire them. Rate-limit scanning through the proxy as you would direct scanning.

## Decision tree (one screen)

1. 3128 open, service banner `http-proxy`/`squid-http` → Phase 1 (state test).
2. Open, no auth → Phase 3 (through-proxy enumeration) immediately.
3. 407 auth required → Phase 2 (credential acquisition) → Phase 3.
4. Any `squid.conf` read (backup/rsync/git) → Phase 2 creds + Phase 4 (cache manager with `cachemgr_passwd`).
5. Internal host found through proxy → exploit it there (its own unit); come back only for more inventory.

## Phase 1 — Fingerprinting the proxy's state

```bash
nmap -sC -sV example.com          # 3128/tcp open http-proxy Squid http proxy 4.11; X-Squid headers
curl --proxy http://example.com:3128 http://example.com/     # self-fetch: open? error flavor?
```

Read the response as one of four states: **open** (forwards anywhere) → Phase 3; **403 Access Denied** (ACL blocks the destination) → map which destinations the ACL *allows*; **Invalid URL** (destination class refused, e.g. localhost) → note it, but chained-loopback may still work (observed); **407 Proxy Authentication Required** → Phase 2. Error pages carry `X-Squid-Error`, the `squid/N.M` version, and the generating host's FQDN — record it as the first internal hostname (observed: an internal `srv01.<domain>` style FQDN from the error footer).

## Phase 2 — Credential acquisition (proxy auth is a password like any other)

Observed sources, in hit order:

- **The Squid user database / config store itself**, reached by another bug: one chain mounted a GlusterFS volume holding the proxy's DB — the `passwd` table (readable via MariaDB or plain `strings` on the file) yielded `user:password` for the proxy; same box kept using `curl --proxy http://user:pass@...:3128` afterwards.
- **`squid.conf` in backups or config repos**: one observed chain decrypted an rsync backup module containing `squid.conf` with an `acl ... intranet.example.com` rule (internal hostname inventory) and inline credentials.
- **Credential reuse from other hosts** in the environment.

Using them: `curl --proxy http://user:pass@example.com:3128 http://target/` — **URL-encode password specials** (`<` `>` `^` `#` `%`) or the userinfo parses wrong (one observed gobuster run needed exactly this); alternatively send `Proxy-Authorization: Basic <b64>` per request. Auth scheme is whatever `auth_param` configured (Basic/Digest/Negotiate — Negotiate implies Kerberos, which means the proxy account is a domain principal worth attacking via the kerberos unit's surface).

## Phase 3 — Through-proxy enumeration (the pivot)

Enumerate as if the proxy were your network position:

```bash
# vhosts: resolve internal names via the proxy's DNS behavior + the box's own services
curl --proxy http://example.com:3128 http://127.0.0.1/            # loopback webapps (observed: default nginx page)
curl --proxy http://example.com:3128 http://intranet.example.com/ # internal vhost named in squid.conf/ACLs
# port/host sweep through the proxy
wfuzz/ffuf via the proxy; or a Go/port-scan tool built for it (squidscan used with proxyURL set, observed)
```

Observed patterns: **hostname-suffix ↔ IP-octet mapping** — when load-balanced backends are named `intranet-host2`, `intranet-host3`, guess the missing host1 at the matching last octet and probe it (one chain found a host "removed for security maintenance" exactly this way); **response-header inventory** — custom `Intranet-Host:` headers name the serving backend per request (observed); **WPAD through the proxy** — the proxy is allowed to fetch `http://wpad.<domain>/wpad.dat` even when other destinations are denied (observed: the PAC file enumerated the proxy fleet); **chained proxies** — if `127.0.0.1:3128` is fetchable through the external proxy, chain onward (`http_access allow localhost` defaults make the loopback hop unauthenticated — observed) and note each hop's version banner.

Interactive testing through the proxy: browser + Burp with **Project Options → Upstream Proxy Servers** pointing at the squid (observed), or proxychains with a dynamic chain listing each hop. Post-exploitation of an internal webapp (SQLi, XPath injection per its own unit) proceeds normally — the proxy only changes your source address.

## Phase 4 — Cache manager abuse (inventory oracle)

The manager is plain HTTP on the proxy port at `/squid-internal-mgr/<action>`, Basic auth where the password is checked against `squid.conf`'s `cachemgr_passwd <password> <action>...` and **the username is ignored** (upstream wiki; older squids also spoke `cache_object://`, removed in 6.5):

```bash
curl -u x:「cachemgr-password」 --proxy off \
  http://example.com:3128/squid-internal-mgr/menu        # list available actions
curl -u x:「cachemgr-password» http://example.com:3128/squid-internal-mgr/fqdncache   # DNS cache
```

Observed chain: `squid.conf`'s `cachemgr_passwd Thah$Sh1 menu pconn mem diskd fqdncache ...` → invoke `fqdncache` → the DNS cache table lists internal hostnames with IPs (`172.31.0.x intranet-host2...`, `172.31.0.y intranet-host3...` — internal IPs from the cache table) → those IPs are fetchable through the proxy → attack the enumerated backends. `menu` always enumerates the action set; `server_list` maps peers; `config` dumps effective config if password-listed. No password set = admin actions denied by default (upstream: "No password. Actions which require password are denied") — but the *reports* (non-starred actions) may still answer.

## Phase 5 — SSRF-via-open-proxy (web-app chaining)

An open proxy is a universal SSRF sink for any other bug that fetches URLs: point a vulnerable webapp's fetch/preview/import feature at `http://proxy:3128` semantics, or when *you* control a request through any intermediary (File > URL import, webhook validators), route internal destinations via the proxy so the *proxy's* egress permissions do the work. In observed engagements the pattern is the inverse — squid as the pivot that makes an app-server-side request look internal — but the same property (the app behind squid trusts loopback/proxy-source requests) is what turns proxy access into app exploitation (observed: loopback nginx → source disclosure → SSTI).

## Disclosure notes

- An open/authenticated proxy that reaches internal ranges is a segmentation failure: document destination classes (loopback, RFC1918, specific vhosts) with request/response pairs, not just "3128 open".
- Cache-manager access = information disclosure of internal topology; rate it against what the leaked hostnames unlock, not the manager itself.
- Never invoke `shutdown`/`reconfigure`/`offline_toggle` — password-gated admin actions on production egress (upstream marks them with `*`).
- WPAD/PAC exposure deserves its own finding: it is how every domain browser can be pointed at attacker infrastructure.

## Appendix A — Tool mapping (prose)

- **nmap** (`-sC -sV`) — service/version detection on 3128; the `X-Squid` error banners do the confirming.
- **curl** (`--proxy`, `-u`, `--upload-file` n/a here) — every phase: state test, credentialed use, cache-manager actions, URL userinfo encoding.
- **wfuzz / ffuf / gobuster** — directory and vhost enumeration through the proxy (remember userinfo URL-encoding when embedding credentials).
- **squidscan** (Go, community-built) — TCP port scanning through a squid proxy by setting its `proxyURL`.
- **proxychains** — chaining multiple proxy hops (dynamic chain) for tools without native proxy support.
- **Burp Suite** — Upstream Proxy Servers config gives Repeater/Intruder through the proxy for interactive webapp testing.
- **MariaDB / strings** — reading the proxy's password table out of its own storage once the box is reachable (observed).
- **dnsenum / DNS tools** — WPAD host discovery (`wpad.<domain>`) once the internal domain is known.

## Supplement (2026-11-13): egress validation, dedicated pivot scanners, and the smuggler-class audit surface

Rewritten from HackTricks (src/network-services-pentesting/3128-pentesting-squid.md), CC BY-NC 4.0, accessed 2026-11-13; tools verified against their repositories and the vulnerability research at its source page before writing.

### Egress validation and the proxy's public identity

Before deep enumeration, establish what the proxy will actually relay: nmap's `http-open-proxy` script is the fast open-proxy check, and one request through the proxy to an echo service (`curl -x http://198.51.100.10:3128 https://ifconfig.me`) both confirms Internet egress and **reveals the egress IP** — useful for scope/attribute questions and for detecting chained egress. A proxy that relays but cannot reach the Internet is still a pure **internal pivot**: proceed against RFC1918 and loopback space.

### Destination classes worth adding to Phase 3

Two target classes the loopback/RFC1918 probes above should explicitly include:

- **Cloud metadata endpoints through the proxy** — `curl -x http://198.51.100.10:3128 http://169.254.169.254/latest/meta-data/` (and the instance-metadata variants per cloud). The proxy is a server-side request forger by trade; if its ACL does not exclude link-local metadata space, whatever cloud role the proxy host holds is the loot.
- **IPv6 loopback and internal TLS via CONNECT** — `curl -x ... http://[::1]:8080/` catches dual-stack admin panels that the IPv4 loopback probe misses, and `openssl s_client -proxy 198.51.100.10:3128 -connect 10.0.0.x:8443 -quiet` forces a CONNECT tunnel and handshakes TLS with internal services that curl's plain-HTTP mode never reaches (`10.0.0.x` = the internal host you enumerated).

### Dedicated pivoting scanners (beyond hand-rolled curl loops)

- **spose** (Squid Pivoting Open Port Scanner) — the classic: `python spose.py --proxy http://198.51.100.10:3128 --target 198.51.100.10` scans ports *on the proxy host itself* (and other reachable hosts) through CONNECT; `--allports` for the full sweep.
- **mysquidscanner** — an asyncio CONNECT scanner that classifies every port as OPEN / BLOCKED (403 from the Squid ACL) / CLOSED / AUTH (407) / TIMEOUT / ERROR and grabs a banner from each open port; the classification makes it the quickest way to fingerprint internal services reachable from the proxy and to tell "filtered by Squid policy" from "closed".
- **proxychains config for HTTP proxies**: the ProxyList entry form is `http <ip> <port> [user pass]` — a strict HTTP entry, not socks4/5. Caveats: proxychains only intercepts TCP `connect()` calls, so run full-TCP-connect scans (`-sT`), avoid SYN/UDP scans through it, and verify hits manually — proxy error responses masquerade as port states.

### Manager action names to add to Phase 4

Alongside `menu`/`fqdncache`/`ipcache`/`server_list`/`config`: `info` (version/build/runtime summary), `counters` (traffic counters), and `active_requests` (in-flight requests with URLs and peers — a live-traffic sample). Same Basic-auth semantics as Phase 4: password checked against `cachemgr_passwd`, username ignored.

### Squid as the target (not just the pivot)

When the engagement audits Squid itself on an outdated or vendor-patched build, add the parser/cache class to the plan: the public "Squid Caching Proxy Security Audit" (Joshua Hu / megamansec, begun Feb 2021 — 55 vulnerabilities, 35 of them 0-days at publication) documents repeated request-smuggling, lenient chunked-decoding, and cache-poisoning style flaws in Squid's HTTP processing. Controlled TE/CL desync tests and cache-variant poisoning checks are in scope against such builds — smuggle findings are their own class (the web-platform units own the general technique), but Squid's history says an old proxy tier deserves them specifically.
