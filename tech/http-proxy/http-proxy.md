---
technology: "HTTP forward proxies (area)"
kind: router
audience: offensive-security
family: network-services
layer: intermediary services that terminate client HTTP(S) and re-origin requests (forward/caching proxies, WPAD/PAC infrastructure)
status: "router — implementation pages split per field evidence"
source: "rewritten from KathanP19/HowToHunt (HTTP_Desync/), GPL-3.0, accessed 2026-11-13"
evidence: "AREA PROBE 2026-11-12: of the placeholder's 15 files (markers: squid, 3128), 5 attack a squid forward proxy itself; zero attack any other forward-proxy implementation; the rest were attacker-side proxychains pivoting (23 files — out of scope, that is post-foothold tooling not a target surface) or collisions ('squid' as a portfolio item name; 'squid' inside an SNMP OID list; a mitmproxy-auth telemetry blob). Gate PASSED (>=5) for a squid implementation unit; no other implementation earned a page. PASS 2 (2026-11-12): haproxy implementation page added under this area — 3 writeups attack HAProxy itself (CVE-2021-40346 smuggling/ACL bypass, CL.TE desync, h2c smuggling + haproxy.cfg loot) + 1 ESI-injection chain behind the front tier; gate >=3 met, see ../internal coverage ledger."
provenance: "area probe over the vault's engagement evidence pool (command-context gate: squid.conf/cachemgr/cache_object/squidclient/X-Squid/squid-N.M banners, 3128/tcp + service names in nmap context, curl --proxy/nc against 3128, /usr/sbin/squid process paths); decision per the vault's area/impl split rule, 2026-11-12. Wave 4 HT enrichment 2026-11-13: 'Proxy-chain techniques' section (hop-by-hop abuse — RFC 9110 §7.6.1 field list verified; connection-state/first-request smuggling; h2c/websocket upgrade tunnels beyond HAProxy with the per-proxy default-forwarding matrix) rewritten from HackTricks (src/pentesting-web/abusing-hop-by-hop-headers.md, http-connection-request-smuggling.md, h2c-smuggling.md), CC BY-NC 4.0, accessed 2026-11-13. PB-INGEST wave 3 (2026-11-13): 'Domain fronting' section rewritten from six2dez/pentest-book (enumeration/cloud/cdn-comain-fronting.md — the typo is the source's filename; no license — rewritten with attribution); prohibition history grounded against the Wikipedia domain-fronting article; Noctilucent mechanism corrected against its repo README (ESNI-based fronting, not session resumption as the source claimed). HTH INGEST (2026-11-13): defparam/smuggler added to the smuggling tooling line, rewritten from KathanP19/HowToHunt (HTTP_Desync/), GPL-3.0, accessed 2026-11-13; the source's remaining content (PortSwigger/process pointers, report and write-up link lists) carried no desync technique the page lacked — the smuggling mechanics were already covered."
---

# HTTP forward proxies (area router)

> A forward proxy is the network's statement about who may talk to what — which makes it both the best pivot in the environment and a credential store with an HTTP interface.

## What this page is

This is the **area page** for attacking HTTP forward proxies — the intermediary services that accept client requests and re-origin them (as opposed to reverse proxies, which front servers and live in the web-platform units). This vault splits multi-implementation areas per implementation when the evidence supports it; the 2026-11-12 area probe found exactly one implementation with real attack evidence:

- **[[squid|Squid]]** — proxy-borne enumeration of internal networks, authenticated-proxy bypass via credential leaks, WPAD/PAC discovery through the proxy, and the cache-manager interface as an internal-inventory oracle. **Start here.**
- **[[haproxy|HAProxy]]** — the reverse-proxy/ACL *front tier* (rather than a forward proxy): fingerprinting and config/stats loot, CVE-2021-40346 htx header smuggling for ACL bypass, CL.TE desync and h2c-upgrade smuggling, response-queue desync (HEAD/TRACE queue poisoning), and ESI injection in the tier behind it. Carried under this area from pass-2 evidence — it is the proxy most often attacked head-on.

No other forward-proxy implementation (tinyproxy, privoxy, 3proxy, mitmproxy, delegate) appears as an attack target in the evidence pool — not as a claim they are unattackable, only that this vault has no evidence to build on. If a future engagement attacks another implementation, it earns its own sibling unit under this area.

## Proxy-chain techniques this area owns

Three request-smuggling-adjacent families apply to ANY front/back proxy pair, not to one implementation — the per-implementation pages carry their HAProxy instances, this section carries the general method.

### Hop-by-hop header abuse

RFC 9110's hop-by-hop fields (`Connection`, `Keep-Alive`, `Proxy-Authenticate`, `Proxy-Authorization`, `TE`, `Trailer`, `Transfer-Encoding`, `Upgrade`) apply to one transport hop and must not be forwarded — and `Connection` NOMINATES additional fields for the same treatment. Client-nominating a security-relevant header (`Connection: X-Forwarded-For`, `Connection: Cookie`) makes a compliant proxy strip it before forwarding, which moves trust boundaries: an earlier hop appended the real client IP, a later hop removes it, and the origin either sees a proxy address (if that hop rebuilds the field from its TCP peer) or nothing (if it does not) — an authorization problem only where downstream code trusts the proxy address or fails open on a missing field. Cache interaction: a nominated field that CHANGES the origin response but is absent from the cache key gets the altered response stored for other users — `Connection: Cookie` alone proves nothing; a poisoning claim needs the response-changing input, the key omission, AND a cacheable response. Test with an echo endpoint and a three-request diff:

```bash
curl --http1.1 -i http://127.0.0.1:8080/echo                                            # baseline
curl --http1.1 -i -H 'X-HBH-Probe: marker' http://127.0.0.1:8080/echo                   # probe present
curl --http1.1 -i -H 'X-HBH-Probe: marker' -H 'Connection: close, X-HBH-Probe' http://127.0.0.1:8080/echo
```

If request three equals request one at the origin, some hop stripped the field — that establishes stripping, not WHICH hop honored your nomination (compare logs on both sides of a proxy-added field for that). Header-injection order and the upstream HTTP version change the outcome; a status-code difference alone is not a bypass.

### Connection-state smuggling (first-request routing and validation)

No CL/TE ambiguity required: the bug is a front-end that decides routing or host-whitelisting ONCE per connection, then reuses the connection for requests with a different `Host`/`:authority`. Two complete requests down one keep-alive socket — first to an allowed host, second to `internal.example` — reach internal vhosts, admin panels, and alternate tenants: an SSRF-shaped primitive that combines with host-header attacks (password-reset poisoning, cache poisoning on the internal vhost). Manual probe:

```bash
printf 'GET / HTTP/1.1\r\nHost: public.example.com\r\nConnection: keep-alive\r\n\r\nGET /admin HTTP/1.1\r\nHost: internal.example.com\r\nConnection: close\r\n\r\n' \
  | openssl s_client -quiet -connect example.com:443
```

Rules of evidence: the second response must belong to the internal host, a FRESH-connection control must NOT reproduce it, and `421 Misdirected Request` in reply is the healthy behavior (the edge noticed and refused). The browser-powered variant rides HTTP/2/3 connection coalescing — one cert valid for both an attacker-controlled hostname and the target, same edge IPs (ORIGIN frames widen what a browser will reuse), and a first-request-only validation; shared CDNs, API gateways, wildcard certs, and Alt-Svc endpoints are the fertile ground.

### Upgrade-tunnel smuggling beyond HAProxy

The h2c mechanics live in [[haproxy|the HAProxy page]] (move 3); what generalizes: in the tested configurations HAProxy, Traefik, and Nuster forwarded `Upgrade: h2c` + `Connection: HTTP2-Settings` by default, while AWS ALB/CLB, NGINX, Apache, Squid, Varnish, Kong, Envoy, and Apache Traffic Server did not (an unsafe config can still pass them). Always also try the NONCOMPLIANT `Connection: Upgrade` variant — some proxy/backend pairs treat it differently — and remember the routing caveat: a path in the upstream URL (`proxy_pass http://backend:9999/socket.io`) constrains nothing once the tunnel exists; every path on `backend:9999` is reachable. The WebSocket cousin smuggles a tunneled connection when front and back DISAGREE whether an upgrade succeeded: send `Upgrade: websocket` with a bad `Sec-WebSocket-Version` — a proxy that forwards it and ignores the backend's `426` rejection holds the connection open to a backend that never accepted it (reproduced on Varnish and Envoy 1.8.0-era); the harder variant rides an SSRF-ish endpoint whose upstream returns `101` to make NGINX believe an upgrade happened. Either way the prize is the same as h2c: raw access to the backend, past path/auth controls enforced only at the front.

Tooling for the family: Burp's HTTP Request Smuggler extension (connection-state probes included), http2smugl (HTTP/2 downgrade paths), smugglefuzz (bulk downgrade probing), h2csmuggler (upgrade tunnelling), and defparam's smuggler (github.com/defparam/smuggler) for bulk front-end desync triage across a target list — `cat hosts.txt | python3 smuggler.py` takes a list, `-u <URL>` scans one host, `-m GET|POST` picks the probe method (default POST), `-x` exits on the first finding per host.

## Domain fronting — the CDN edge as cover traffic

The same edge-routing divergence the smuggling families exploit, seen from the egress side. A destination name appears in three places on an HTTPS request — the DNS query, the TLS SNI extension, and the HTTP `Host` header — and ordinarily all three agree. A CDN edge terminates TLS on one tenant's name and routes on the `Host` of another: point SNI and DNS at a legitimate tenant, put your own hostname in `Host`, and everything between the client and the edge (the proxy, the middlebox, the country) records a conversation with the legitimate domain while the payload is yours. The standard that defines SNI discourages the mismatch; it never forbade it — the CDNs' own routing behavior is what made it work.

**It is mostly dead as a default move — verify before building on it.** The large edges prohibit and now enforce the mismatch: Google Cloud and Amazon CloudFront disabled fronting in 2018 under censor pressure, and Microsoft actively prohibits it (enforcement varies by product and has tightened repeatedly since). Cloudflare has refused mismatched plaintext/encrypted destination pairs since 2020. Treat every per-CDN claim, including the above, as a policy artifact with a shelf life, and probe the actual edge in scope:

```bash
curl -s -H "Host: your-host.example.net" https://legitimate-tenant.example.net/ | head -1
# your content back  -> the edge still routes on Host alone, fronting works
# 421 / 403 / TLS error -> mismatch enforced; that door is closed
```

Both names must live on the same edge AND both be served by it — a shared `*.cloudfront.net`-class suffix is the historical shape; per-vendor edge hostnames (`*.azurefd.net`, `*.azureedge.net`, `*.fastly.net` and friends) are where candidates come from. Discovery tooling (FindFrontableDomains, DomainFrontingLists and similar repos) mostly enumerates stale candidates — the probe above is the ground truth.

**The TLS 1.3 successor, and why it died too.** Noctilucent (DEF CON 28, "Domain Fronting is Dead, Long Live Domain Fronting") moved the hidden name into TLS 1.3 **ESNI**: SNI names the front, the encrypted SNI extension carries the real server name, `Host` stays honest — nothing plaintext to compare. Cloudflare broke the bypass days after the talk by refusing any ClientHello carrying both ESNI and SNI. The successor standard, ECH, re-runs the same race per edge: an ECH-enabled front that serves your inner name is a live experiment, never an assumption.

**C2 wiring** is a header override, not code: Cobalt Strike malleable profiles set the inner `Host` per transaction (`header "Host"` in the profile), and Metasploit's reverse-HTTPS payloads carry the same idea as a `HttpHostHeader` option — the front domain is listener metadata. Sliver-class implant servers expose the equivalent Host-header override on their HTTP(S) listeners.

**When fronting fails, the neighbors work:** a CDN-as-redirector (an edge rule, Worker, or edge function proxying to your real listener — SNI and `Host` now agree, only the IP is CDN), serverless dead-drops (object storage as staging), and "domain hiding" variants where the encrypted request rides under a plaintext one. All of them buy a routing illusion, not content invisibility — **what burns any of them is the same triplet**: TLS-inspecting middleboxes that compare SNI to `Host` per request, CDN edge logs that tie the connection to the inner name regardless of SNI, and traffic shape (beacon-timed POSTs to a static-content tenant). From the defender's chair, the `421 Misdirected Request` behavior in the smuggling section above is the same signal — an edge that notices name divergence and refuses.

## What the area means on an engagement

An open forward proxy converts "I can reach the proxy" into "the proxy can reach everything it fronts" — internal vhosts, loopback-bound admin panels, adjacent subnets the ACL names. An *authenticated* proxy is the same surface behind a password that leaks from config files, credential databases, or other boxes' backups. The methodology file for squid covers: enum through the proxy, cache-manager abuse, SSRF-via-proxy patterns, and proxy auth handling.

## Where to go next

[[squid|Squid overview]] and [[squid-bug-bounty|Squid bug bounty methodology]].
