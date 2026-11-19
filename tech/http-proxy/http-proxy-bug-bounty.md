---
technology: "HTTP proxies (open-proxy and proxy-trust methodology)"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
version: "1.0"
last-refresh: "2026-11-19"
status: upstream-only
provenance: "Drafted 2026-11-19 (claude-opus-4-8) against the house standard [[fastapi-bug-bounty]]; companion overview [[http-proxy]]. Commands verified against curl's proxy documentation (everything.curl.dev/usingcurl/proxies, --proxytunnel man page), PortSwigger web-cache-poisoning research, and 2025 reverse-proxy trust CVEs (Fabio CVE-2025-48865, OAuth2-Proxy CVE-2025-64484, 1Panel CVE-2025-66508). CVE-2012-2213 (Squid CONNECT/Host ACL bypass) cited as one-line history only."
source: "WebSearch/WebFetch research 2026-11-19: curl proxy docs, PortSwigger cache-poisoning/deception, Rapid7 open_proxy module, nmap http-open-proxy, 2025 forwarding-header CVE advisories. Technique framing adapted to the vault's authorized-program discipline."
---

Companion pages: [[http-proxy|HTTP forward proxies (area overview)]] — scope, the proxy-chain/smuggling families, and domain fronting live there. Implementation-specific attacks on Squid itself live in [[squid-bug-bounty|the Squid methodology]]. Tool notes live in the separate tools vault. This file is the **proxy-trust and open-proxy** methodology — it treats the proxy as the target and the proxy's trust decisions as the bug surface, across any implementation.

---

# HTTP proxy bug-bounty methodology — open proxies and proxy trust

**In scope:** forward proxies you can reach (open or authenticated), reverse proxies and their header-trust chains, caching proxies as a data-leak surface, and transparent/intercepting proxies as an interference finding. The product is the *trust question*: who does this proxy believe, and what does the backend behind it believe the proxy already checked.

**Out of scope here:** request smuggling / desync (CL.TE, h2c, hop-by-hop) and domain fronting — those live in [[http-proxy]]; Squid implementation internals (cachemgr, WPAD/PAC) — those live in [[squid-bug-bounty]].

**Authorized-program discipline (non-negotiable):** every open-proxy probe goes to **your own canary URL only** — never relay through a found-open proxy to a third party you do not control, because that is using someone else's infrastructure to attack someone else. One probe per pattern. Prove reachability; never exfiltrate. Cloud-metadata and internal-service findings are **REPORT-class** — you demonstrate the door opens, you do not walk through and loot.

## Where the money is (triage priority)

| Priority | Class | Shape | Typical severity |
|---|---|---|---|
| 1 | SSRF pivot via open forward proxy | Proxy relays your canary request, then reaches internal/loopback services | High–Critical |
| 2 | Trust-header auth bypass | Backend trusts client `X-Forwarded-For`/`X-Real-IP` → IP allowlist / `127.0.0.1` admin / rate-limit reset | High |
| 3 | Path-override ACL bypass | `X-Original-URL` / `X-Rewrite-URL` reaches `/admin` past a front-tier path ACL | High |
| 4 | `X-Forwarded-Host` reflection | Poisoned password-reset links, open redirect, cache-key divergence | High |
| 5 | Caching proxy leaks authenticated data | Private response stored in a shared cache, served to anonymous users | Medium–High |
| 6 | Web cache poisoning via unkeyed header | Response-changing header absent from the cache key, cacheable response | Medium–High |
| 7 | Open CONNECT relay | Proxy tunnels arbitrary `host:port` for you | Medium–High |
| 8 | Header-normalization confusion | Underscore vs hyphen, `Connection`-nominated strip, default-trusted-proxy | Medium |
| 9 | Proxy-auth (407) bypass | Path/method/case tricks past a credentialed-proxy ACL | Medium |
| 10 | Transparent-proxy interference | In-path TLS swap or header injection on a segment you observe | Low–Medium (hardening) |
| 11 | Open relay without internal reach | Pure egress proxy, nothing internal behind it | Low–Medium |

Phases 0–1 establish *what kind of proxy* you are looking at; Phases 2–3 are the open-forward-proxy and SSRF-pivot payouts; Phases 4–6 are the reverse-proxy trust, caching, and transparent-interference surfaces.

---

## Phase 0 — Classify the proxy before you probe it

Goal: decide whether the thing in front of you is a **forward** proxy (accepts absolute-URI / CONNECT for *arbitrary* destinations — the open-proxy surface) or a **reverse** proxy (fronts one set of backends — the trust-header surface). They fail in opposite ways and the probes differ.

### 0.1 The request-form tell

Origin servers want origin-form (`GET /path HTTP/1.1`). A forward proxy accepts **absolute-form** in the request line (`GET http://host/path HTTP/1.1`) and the **CONNECT** method. curl selects the right form automatically when you pass `-x`:

```bash
# Absolute-URI probe to YOUR canary through the suspect proxy (http)
curl -sS -x http://198.51.100.10:3128 http://canary.example.net/probe-0 -o /dev/null -w '%{http_code}\n'
# CONNECT-tunnel probe to YOUR canary (https) — forces a CONNECT
curl -sS -x http://198.51.100.10:3128 https://canary.example.net/probe-0 -o /dev/null -w '%{http_code}\n'
```

A reverse proxy answers only for the hosts it fronts and returns an error (`400`, `403`, `502`) when asked to re-origin an arbitrary absolute URI. A forward proxy fetches it. **The verdict is not the status code — it is whether your canary logged the hit** (Phase 2).

### 0.2 Banner and behavior fingerprints

```bash
curl -sI http://198.51.100.10:3128/ | grep -iE 'via|server|x-cache|x-squid|proxy-'
```

`Via:` names the proxy software and version (`Via: 1.1 squid (squid/6.6)`); `X-Cache`, `Age`, `X-Cache-Lookup` mark a caching proxy; `Server: squid`/`Proxy-Connection` are forward-proxy tells. `407 Proxy Authentication Required` with `Proxy-Authenticate:` means a credentialed forward proxy (Phase 3.4). Absence of all of these on a service that still re-origins is a stripped-header forward proxy — treat the canary result as ground truth.

### 0.3 Service discovery (one light pass)

Forward proxies cluster on `3128` (Squid), `8080`, `8000`, `1080`, `8888`. Fingerprint, do not flood:

```bash
nmap -Pn -sV -p 3128,8080,8000,8888,1080 --script http-open-proxy --script-args http-open-proxy.url=http://canary.example.net/nse-probe 198.51.100.0/24
```

`http-open-proxy` issues both a GET-through and a CONNECT and reports `Potentially OPEN proxy`. Point its test URL at your canary so the NSE's own request is evidence, not noise against a third party.

---

## Phase 1 — Open forward-proxy detection (strict canary discipline)

Goal: prove the proxy will re-origin to a destination of *your* choosing — and prove it with a log line on infrastructure you own, never with a relayed request to anyone else.

### 1.1 Stand up the canary first

You need an out-of-band listener whose hit log is your evidence. In order of preference: **interactsh-client** (`interactsh-client -v` prints an `https://<id>.oast.fun` host and logs every HTTP/DNS hit with source IP and timestamp), **Burp Collaborator**, or your own HTTP logger on in-scope infrastructure. The canary host is the *only* destination you send through an untrusted proxy.

```bash
interactsh-client -v
# -> [INF] Listing 1 payload for OOB Testing
# -> c9x...oast.fun
# every proxied request to that host prints:  [c9x...] Received HTTP interaction from 203.0.113.50 at 2026-11-19 ...
```

### 1.2 The CONNECT-method matrix (one probe per pattern)

Each row is a distinct trust question. Send each once, to your canary only.

| # | Pattern | Command (canary = your OAST host) | OPEN if |
|---|---|---|---|
| 1 | Absolute-URI GET (HTTP relay) | `curl -x http://P:3128 http://CANARY/p1` | canary logs `/p1` from the proxy's egress IP |
| 2 | CONNECT :443 (HTTPS tunnel) | `curl -x http://P:3128 https://CANARY/p2` | canary logs the TLS hit |
| 3 | CONNECT :80 (forced tunnel) | `curl --proxytunnel -x http://P:3128 http://CANARY:80/p3` | raw tunnel established to :80 |
| 4 | CONNECT to a non-web port | `curl --proxytunnel -x http://P:3128 http://CANARY:2525/p4` | arbitrary-port CONNECT → relay/pivot risk |
| 5 | `--proxy1.0` (legacy CONNECT) | `curl --proxy1.0 http://P:3128 https://CANARY/p5` | HTTP/1.0 CONNECT still honored |

```bash
# Pattern 4 verbatim — proves arbitrary-port CONNECT (spam-relay / internal-pivot primitive)
curl -sS --proxytunnel -x http://198.51.100.10:3128 http://canary.example.net:2525/p4 \
  -o /dev/null -w 'tunnel status: %{http_code}\n'
# -> 200  (proxy said "200 Connection established"); then check the canary log for the :2525 hit
```

`--proxytunnel` (`-p`) forces a CONNECT even for an `http://` URL — it is how you test whether the proxy will open a raw tunnel to an arbitrary port rather than just fetch web content. `--suppress-connect-headers` hides the proxy's own `200 Connection established` if it clutters `-i` output. **Stop after one probe per row.** A `200` from the proxy is a claim; the canary log line is the proof.

### 1.3 Decision point

- **No canary hit on any row** → not an open forward proxy. If it is a reverse proxy, go to Phase 4.
- **Canary hit on rows 1–2 only** → open HTTP/HTTPS relay. Reportable (Priority 7/11); now test whether anything *internal* is reachable (Phase 2) to lift severity.
- **Canary hit on row 4** → arbitrary-port CONNECT. The proxy is a general-purpose pivot and spam relay — higher severity even before internal reachability.

---

## Phase 2 — SSRF-pivot value of an open proxy

Goal: an open forward proxy converts "I can reach the proxy" into "the proxy can reach everything it fronts." This is where Priority-1 severity comes from. **Everything here is REPORT-class: prove reachability, never loot.**

### 2.1 Internal service reachability proof

The proof pattern is always **reach-through + negative control**: a response from a destination you demonstrably cannot reach directly.

```bash
# Through the open proxy — internal/loopback target (use only in-scope internal ranges)
curl -sS -x http://198.51.100.10:3128 http://127.0.0.1:8080/ -o proxied.txt -w '%{http_code}\n'
# Negative control — same target, no proxy, from your own host
curl -sS --max-time 5 http://127.0.0.1:8080/ -o /dev/null -w '%{http_code}\n'   # must fail / time out
```

Evidence = a banner, title, or body from an internal-only service in `proxied.txt` **plus** the failing direct control. One request per internal target; do not port-scan the internal network through the proxy unless the program explicitly authorizes it — a sweep is high-volume and exactly the discipline line this vault holds.

### 2.2 Cloud metadata — REPORT-class, do not dump credentials

The link-local metadata endpoint `169.254.169.254` is reachable from inside most cloud networks and is the highest-signal internal target. The finding is **reachability**, demonstrated with the least-sensitive response that proves it:

```bash
# IMDSv1 reachability proof — a directory listing, NOT the credentials path
curl -sS -x http://198.51.100.10:3128 http://169.254.169.254/latest/meta-data/ -w '\n%{http_code}\n'
# -> 200 and a newline-separated index (ami-id, hostname, iam/, ...) = metadata reachable through the proxy
```

Stop at the index. Do **not** fetch `.../iam/security-credentials/<role>` — that returns live IAM keys and pulling them is over-collection you cannot justify in a report. If the response is `401`/`403` on a GET, the target enforces **IMDSv2** (a session token via `PUT /latest/api/token` is required) — note that as a mitigating control; do not then forge the PUT to prove the point. Report the reachability and let the program decide.

### 2.3 Severity ladder

Open relay with no internal reach → Low–Medium. Open relay reaching an internal HTTP service → High. Open relay reaching cloud metadata → Critical (even without dumping creds, because the next request would). Say exactly which rung you proved and which you deliberately did not climb.

---

## Phase 3 — Open-proxy edge cases and auth bypass

### 3.1 Protocol and scheme reach

Through an open CONNECT proxy, non-HTTP destinations become reachable. Test — against your canary only — whether the tunnel carries raw bytes (a canary listener on an arbitrary port that echoes the first line proves a non-HTTP tunnel). Do not aim gopher/raw-TCP primitives at anything you do not own.

### 3.2 Proxy-auth (407) bypass families

A credentialed forward proxy answers `407` without `Proxy-Authorization`. Bypass attempts, one each:

- **Method/case tricks** against a CONNECT ACL: some ACLs gate `CONNECT` but not absolute-URI `GET`, or match method case-sensitively.
- **Host-header vs request-line divergence** on CONNECT ACLs — the historical shape. *CVE-2012-2213* (Squid ≤ 3.1.9, disputed, unpatched) let a client pass an allowed hostname in the `Host` header to slip a CONNECT past a Host-regex ACL. One line of history; its value today is the *pattern* — when an ACL keys on one field and routing keys on another, look for the gap.
- **Path/encoding ACL bypass** on proxies that filter by URL: double-slash, `%2e%2e`, trailing dot in the authority, mixed case. Compare an ACL-blocked path to an encoded variant and diff.

### 3.3 Credential leaks that turn authenticated into open

An authenticated proxy is the same surface behind a password that leaks from config backups, `squid.conf`, `.netrc`, CI logs, or another box. If you recover `user:pass`, `curl -x http://user:pass@P:3128 ...` (or `--proxy-user`) and the whole of Phases 1–2 applies.

---

## Phase 4 — Reverse-proxy trust chains (the header-spoof class)

Goal: the single most productive reverse-proxy question — **which backend trusts which header, and does the edge strip the client's copy before forwarding?** A header the client should never control, honored at the backend, is the finding.

### 4.1 Forwarded-identity headers (IP trust)

Backends routinely derive "the client's IP" from a header instead of the TCP peer. Spoof it and watch for authorization or rate-limit changes:

```bash
# Reach an IP-gated admin surface by claiming to be loopback
curl -sS -i https://app.example.com/admin -H 'X-Forwarded-For: 127.0.0.1'
curl -sS -i https://app.example.com/admin -H 'X-Real-IP: 127.0.0.1'
# Rate-limit reset: rotate a forged client IP after exhausting your own
for ip in 203.0.113.1 203.0.113.2 203.0.113.3; do
  curl -sS -o /dev/null -w "%{http_code} xff=$ip\n" https://app.example.com/auth/login \
    -H "X-Forwarded-For: $ip" -d 'u=alice&p=x'
done
```

Test the family: `X-Forwarded-For`, `X-Real-IP`, `X-Client-IP`, `True-Client-IP`, `CF-Connecting-IP`, `Forwarded: for=`. Finding signals: `403→200` on an IP-gated route, a rate limit that resets per forged IP, or an audit log that records your spoofed value. *CVE-2025-66508* (1Panel) is the canonical current shape — a framework (Gin) whose **default trusted-proxy setting trusts every client**, so any caller's `X-Forwarded-For` is believed. The spoof-finding class is exactly "the default trusts the client."

### 4.2 Forwarded-host / scheme headers (URL and routing trust)

`X-Forwarded-Host`, `X-Forwarded-Proto`, `X-Forwarded-Server`, and bare `Host` flow into absolute-URL generation:

```bash
# Password-reset poisoning — does the reset link adopt the attacker host?
curl -sS -i https://app.example.com/password-reset \
  -H 'X-Forwarded-Host: attacker.example' -d 'email=victim@example.com'
# Then read the delivered reset mail / response body for attacker.example in the link
```

A reset link, OAuth redirect, or OG-meta URL built from `X-Forwarded-Host` is a token-theft chain (the 2023-era Laravel `TrustHosts`-off pattern, still endemic). This is also the input to Phase 5 cache poisoning.

### 4.3 Path-override headers (ACL bypass)

Some front tiers route on `X-Original-URL` / `X-Rewrite-URL` (Symfony, certain proxy configs). If the edge enforces a path ACL on the request line but the backend re-routes on the override header, you reach the protected path with an innocent line:

```bash
curl -sS -i https://app.example.com/ -H 'X-Original-URL: /admin/settings'   # front ACL sees "/", backend serves /admin/settings
```

### 4.4 Header-normalization confusion — the trust-chain seams

The edge's stripping logic and the backend's parsing disagree:

- **Hyphen vs underscore:** *CVE-2025-64484* (OAuth2-Proxy) — the sanitizer stripped `X-Forwarded-Email` but not `X_Forwarded_Email`; the underscore variant reached the backend. Always test the underscore twin of any security header.
- **`Connection`-nominated stripping:** `Connection: X-Forwarded-For` makes a compliant hop drop the field (see the hop-by-hop section in [[http-proxy]]); *CVE-2025-48865* (Fabio) let clients nominate and strip the proxy's *own* security headers this way. If stripping a forwarding header changes the backend's trust decision, that is the finding.
- **Duplicate headers:** send two `X-Forwarded-For` lines and see which the backend picks (first, last, or joined) — edge and backend disagreeing on which copy is authoritative is a spoofing primitive.

The deliverable from Phase 4 is a small table: `header × route × effect`, and for each honored header whether the edge stripped a client-supplied copy (it should) or passed it through (it should not).

---

## Phase 5 — Caching proxies as a data-leak surface

Goal: a shared cache in front of authenticated content is a cross-user disclosure engine. Two distinct bugs: the cache **storing** private data, and an attacker **poisoning** what others receive.

### 5.1 Cached authenticated responses (disclosure)

If a per-user response is cached under a URL-only key, the next anonymous request gets it. Test with the cache-status headers and a two-identity diff:

```bash
# 1) Authenticated request primes the cache
curl -sS -i https://app.example.com/account -H "Cookie: session=$VICTIM" | grep -iE 'x-cache|age|cache-control|set-cookie'
# 2) Anonymous request to the same URL — does it return the primed private body?
curl -sS -i https://app.example.com/account | grep -iE 'x-cache|age'
```

Finding: an anonymous `X-Cache: HIT` returning another user's data, or `Cache-Control: public`/missing on an authenticated response. **Web cache deception** is the path-confusion cousin: `GET /account/nonexistent.css` — if the proxy caches it as a static asset but the app serves your account page, a victim-primed version becomes attacker-readable. One probe; do not retrieve more of a victim's data than the single body that proves the leak.

### 5.2 Cache poisoning via an unkeyed header (one line, with the standard caveat)

A header that **changes the response** but is **absent from the cache key**, on a **cacheable** response, lets you store a malicious variant for everyone — e.g. `X-Forwarded-Host: attacker.example` reflected into a `<script src>` and cached. The three conditions must all hold; `X-Forwarded-Host` alone proves nothing. Burp's **Param Miner** finds the unkeyed inputs (it adds a cache-buster automatically so it only flags response-changing headers). Prove it with a cache-busted probe, then confirm the poisoned entry serves to a clean request — and keep the payload benign (a canary host, not a live redirect) so no real user is harmed.

---

## Phase 6 — Transparent / intercepting proxy interference

Goal: an in-path proxy the client did not configure. On a segment you are authorized to observe, it is a finding when it weakens the connection.

- **TLS interception:** the presented leaf cert is issued by an injected CA, not the real origin. Capture and inspect the chain with **mitmproxy** (analysis mode) or `openssl s_client -connect host:443 | openssl x509 -noout -issuer`. An unexpected issuer on a segment that should be end-to-end TLS is the evidence.
- **Header injection:** a transparent proxy that adds `Via`, `X-Forwarded-For`, or `X-Cache` — compare the request the origin logs against what the client sent. Injected forwarding headers feed straight back into the Phase 4 trust bugs.
- **Request/response mutation:** diff a request sent direct (where you have a path around the proxy) against the same request through the transparent path. Any rewrite — downgraded TLS, stripped security headers, altered caching directives — is reportable; in most bounty programs as hardening, in an authorized assessment often as the headline deployment finding.

Use **mitmproxy**/`mitmdump` as the analysis lens throughout: run your own traffic through it to see exactly what headers a proxy adds, strips, or rewrites before you attribute behavior to the target.

---

## Reporting and evidence standards

The report is the product. Structure every finding impact-first, PoC-tight, one request per step.

| Finding class | What the proof MUST contain |
|---|---|
| Open forward proxy | The **canary hit log line** — timestamp, the path you requested, and the **proxy's egress IP** as source. A `200` from the proxy alone is not proof. |
| SSRF pivot (internal) | Reach-through response body/banner from an internal-only target **plus** the failing direct-access negative control. |
| Cloud-metadata reachability | The metadata **index** response (REPORT-class); an explicit note that credentials were not fetched. |
| Trust-header spoof | Before/after with the header (e.g. `403→200`, or the attacker host in a generated link), with a fresh control request. |
| Cache data leak | The private body retrieved under no/other credentials, with the `X-Cache: HIT`/`Age` header shown. |
| Cache poisoning | The response-changing input, the key omission, a cacheable response, and a clean request receiving the (benign) poisoned variant. |
| Transparent interference | Captured chain/headers showing the injected CA or added/stripped header, naming the observed segment. |

Severity: SSRF pivot to internal services — High; pivot reaching cloud metadata — Critical; trust-header auth bypass or reset-link poisoning — High; cached authenticated data served cross-user — Medium–High; open relay with no internal reach — Low–Medium; transparent-proxy hardening — Low–Medium. Always state the rung you proved and the rung you deliberately did not climb — that boundary is what keeps the finding deployable and the report honest.

### Falsifiable closeout

| Claim you tried to prove | Rejected (secure) if |
|---|---|
| The proxy re-origins to a destination I choose | no canary hit on any Phase-1 row |
| The open proxy reaches something internal | every internal/metadata probe fails or times out through the proxy |
| The backend trusts a client-supplied identity header | every forwarding header is ignored or stripped at the edge |
| A shared cache stores or serves private data | no anonymous `HIT` returns authenticated content; no unkeyed response-changing header |
| An in-path proxy weakens the connection | observed TLS chain and headers match end-to-end expectation |

---

## CTF notes

- **Open proxy → localhost flag service.** The classic: a box exposes `3128` and a flag service bound to `127.0.0.1`. `curl -x http://box:3128 http://127.0.0.1:<port>/` reads it — the proxy *is* the SSRF.
- **`X-Forwarded-For: 127.0.0.1`** (and the whole family) is the first thing to try against any "admin only from localhost" gate — CTF reverse proxies almost always trust the raw header.
- **`X-Original-URL: /admin`** when the front returns `403` on `/admin` but `200` on `/`.
- **Absolute-URI to reach a hidden vhost:** `curl -x http://box:3128 http://internal-vhost/` when the vhost is name-based and not routable directly.
- **Metadata in cloud CTFs** is fair game end-to-end — fetch `.../iam/security-credentials/` for the role creds; that is the flag path there, unlike a live program where you stop at the index.
- **Cache deception** on a scoreboard: `GET /profile/me.css` sometimes caches and exposes the admin's primed profile.

---

## References

- curl — HTTP proxy usage (`-x`/`--proxy`, absolute-URI vs CONNECT): https://everything.curl.dev/usingcurl/proxies/http
- curl — `--proxytunnel` / `-p` man page: https://curl.se/docs/manpage.html#-p
- PortSwigger — Web cache poisoning (unkeyed inputs, Param Miner): https://portswigger.net/web-security/web-cache-poisoning
- PortSwigger — Practical Web Cache Poisoning (research): https://portswigger.net/research/practical-web-cache-poisoning
- nmap — `http-open-proxy` NSE script: https://nmap.org/nsedoc/scripts/http-open-proxy.html
- Rapid7 — `auxiliary/scanner/http/open_proxy`: https://www.rapid7.com/db/modules/auxiliary/scanner/http/open_proxy/
- interactsh (OAST canary): https://github.com/projectdiscovery/interactsh
- mitmproxy — interception/analysis: https://docs.mitmproxy.org/stable/
- CVE-2025-64484 — OAuth2-Proxy header-normalization (underscore) bypass: https://github.com/oauth2-proxy/oauth2-proxy/security/advisories
- CVE-2025-48865 — Fabio `Connection`-header strip of forwarding headers: https://github.com/fabiolb/fabio/security/advisories
- CVE-2025-66508 — 1Panel/Gin default-trusted-proxy X-Forwarded-For spoofing: https://nvd.nist.gov/vuln/detail/CVE-2025-66508
- CVE-2012-2213 (one-line history) — Squid CONNECT/Host ACL bypass (disputed): https://nvd.nist.gov/vuln/detail/CVE-2012-2213
- AWS — IMDSv2 (why a GET returns 401 without a token): https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-service.html

*Drafted 2026-11-19 by claude-opus-4-8, against the house standard [[fastapi-bug-bounty]] and the companion overview [[http-proxy]]. Sources consulted and verified this date: curl proxy documentation (everything.curl.dev, `--proxytunnel` man page), PortSwigger web-cache-poisoning research, nmap `http-open-proxy`, Rapid7 `open_proxy`, and the 2025 forwarding-header advisories (OAuth2-Proxy CVE-2025-64484, Fabio CVE-2025-48865, 1Panel CVE-2025-66508); CVE-2012-2213 cited as history only.*
