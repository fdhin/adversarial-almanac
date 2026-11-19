---
name: "mitmproxy"
categories:
  - sniffing-spoofing
  - web
version: "12.3.1-1"
homepage: "https://mitmproxy.org"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-10-26"
build-system: python
meta-checked: "2026-11-09"
github-repo: "mitmproxy/mitmproxy"
github-stars: 46702
github-pushed: "2026-11-04"
github-archived: false
github-issues: 542
github-match: "search"
gh-checked: "2026-11-07"
worth: 9.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 239
gh-activity-checked: "2026-11-09"
---

# mitmproxy

> Interactive TLS-capable intercepting proxy for HTTP/1, HTTP/2 and WebSockets, with console, web and dump frontends.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Seeing and modifying traffic that normally hides inside TLS. mitmproxy terminates TLS on behalf of the real endpoints (after the client trusts its CA) and exposes complete flows — request, response, headers, bodies, timing — for inspection and scripted rewriting. It ships as three frontends over one engine: `mitmproxy` (interactive terminal UI), `mitmweb` (browser-based GUI) and `mitmdump` (non-interactive, tcpdump-style output). A single `--mode` option switches how traffic is lured in: explicit proxying, local per-process capture, reverse proxying in front of a server, transparent interception, SOCKS5, WireGuard, TUN or DNS server. Extendable with Python addons. The catch is trust: intercepting TLS requires installing mitmproxy's CA certificate on the client, and traffic pinned or otherwise refusing the CA stays opaque.

## Typical use cases

### Proxy a client through it interactively

```
mitmproxy
```

Outcome: starts a regular HTTP proxy on localhost:8080; point a browser or tool at it, open the magic domain `mitm.it` through the proxy and install the CA certificate for the client platform, then every flow — including HTTPS once the CA is trusted — appears in the terminal UI for inspection and modification. `mitmweb` gives the same capture in a browser GUI, `mitmdump` a non-interactive flow listing.

### Capture a single process without proxy settings

```
mitmproxy --mode local:curl
```

Outcome: transparently intercepts only the matching process's outbound traffic (local capture mode), with no proxy configuration needed in the application; `--mode local` captures everything on the machine, and specs can be negated or listed — `local:!curl,wget` intercepts everything except those two.

### Put it in front of a server as a reverse proxy

```
mitmdump -p 443 --mode reverse:https://example.com
```

Outcome: mitmproxy listens on port 443 and forwards to the fixed upstream, logging (and letting you rewrite) every client session — useful for debugging an API by pointing clients at the proxy instead of the real server; the scheme selects the protocol, so `reverse:tcp://example.com:1234` proxies raw TCP.

### Intercept traffic that cannot use a proxy

```
mitmdump --mode transparent
```

Outcome: transparent mode for Linux/macOS gateway setups where traffic is routed to the proxy box at the network layer (e.g. clients using it as default gateway) — no client-side proxy configuration is possible or needed; install the CA via `mitm.it` as usual.

### Act as a SOCKS5 proxy

```
mitmdump --mode socks5
```

Outcome: clients that speak SOCKS5 (SSH dynamic forwarding, some browsers and CLI tools) connect through mitmproxy without HTTP proxy settings and their flows show up like any other capture.

### Chain behind another proxy

```
mitmdump --mode upstream:http://example.com:8081
```

Outcome: every intercepted request is unconditionally forwarded through the upstream HTTP proxy — for layering mitmproxy in front of a corporate proxy appliance or another mitmproxy instance.

---

## Bug bounty workflow with mitmproxy

mitmproxy is not a scanner and finds nothing on its own. It is the **observation-and-manipulation layer** the rest of your toolkit sits on: it shows you exactly what a client sends inside TLS, lets you edit any field and resend, and — through Python addons — turns that manual loop into a scoped, rate-capped repeater/intruder. On an authorized program its highest-value use is the surface no web proxy sees for you automatically: a mobile app's real API traffic, a native client's WebSocket channel, an mTLS-gated endpoint. Everything below assumes written permission, a defined scope, a **dedicated test device or emulator** (never a personal phone — you are decrypting *all* of that device's TLS), and single-request/low-volume discipline. Where a step can flood a target or capture out-of-scope data, the safe setting is called out inline.

Version note: grounded against mitmproxy **12.3.1** (2026-10-25). Flags, modes, the addon event API, the console keymap, and HAR support are current as of that release.

### Phase 0 — Trust, confinement, and scope fencing

Goal: intercept *only* in-scope traffic, with a CA you control, before you look at a single flow.

1. **Use a throwaway config dir** so a target engagement's CA, keys, and saved flows never mix with another's: `mitmdump --set confdir=~/engagements/accme/.mitmproxy`. mitmproxy generates its CA there on first run (`mitmproxy-ca.pem` = cert+key, `mitmproxy-ca-cert.pem` = cert for most platforms, `.cer` for Android, `.p12` for Windows).
2. **Install the CA on the client** via the magic domain `mitm.it` (reachable only *through* the running proxy), or manually from the files above. This is the whole trust model: traffic whose client does not trust your CA — or that pins a certificate — stays opaque.
3. **Fence the scope immediately.** Local/transparent capture grabs *everything* the machine emits. Exclude everything you are not authorized to decrypt with `ignore_hosts` (TLS passthrough, no decryption) and confirm with a filter view:
   ```
   mitmdump --set confdir=~/engagements/acme/.mitmproxy \
            --ignore-hosts '^(?!.*\.example\.com:443$)'   # only decrypt *.example.com:443
   ```
   `ignore_hosts` takes a host:port regex; anything matching is tunnelled untouched. This is both a scope control and a privacy control — it keeps the bug-tracker login, the OS telemetry, and any personal app out of your capture.

### Phase 1 — Passive capture and surface mapping

Goal: build an inventory of hosts, endpoints, parameters, and auth channels *without sending a single crafted request*. Passive-first is the discipline that keeps early recon dupe-proof and quiet.

Pick the lure that fits the client:

| Client type | Mode | Command |
|---|---|---|
| Browser / proxy-aware CLI | regular (explicit proxy :8080) | `mitmweb` |
| One desktop process, no proxy config | local capture | `mitmdump --mode local:thunderbird` |
| Mobile app, no root | WireGuard (on-device VPN) | `mitmweb --mode wireguard` |
| Mobile app, rooted / emulator | transparent or WireGuard | `mitmdump --mode transparent --showhost` |
| Debugging one API | reverse | `mitmdump --mode reverse:https://api.example.com` |

Let the client exercise its features while `mitmweb`/`mitmproxy` records. Then mine the flow list with the filter language (same tokens in the `:` command bar, the `f` view-filter, and as a trailing `mitmdump` argument):

```
~d example.com & ~m POST        # POSTs to the target domain
~u /api/v[0-9]+/                # versioned API paths
~hq Authorization               # requests that carry an auth header
~t json & ~bs "password|token"  # JSON responses whose body leaks secrets
~websocket                      # WebSocket flows
```

Snapshot the baseline to disk (`w` in the console, or `-w`) — it is your diff reference and your evidence seed. **Mobile capture is where dupes are rarest:** the app's private API, its undocumented endpoints, and the parameters a web UI never exposes are under-tested precisely because they are invisible without a tool like this.

### Phase 2 — The capture-to-replay loop (the manual backbone)

This four-key loop *is* mitmproxy as a testing tool — a Repeater you never leave:

| Key | Command | What it does |
|---|---|---|
| `enter` | `console.nav.select` | open the focused flow (request/response, headers, body, timing) |
| `D` | `view.flows.duplicate @focus` | clone the flow so the original stays as your baseline |
| `e` | edit → choose component | edit method, path, query, headers, or body in `$EDITOR` |
| `r` | `replay.client @focus` | resend the (edited) request; the row updates in place |
| `w` | `console.command save.file @focus` | save this flow to a `.flows` file as evidence |

The loop for a BOLA probe: focus your own `GET /invoices/inv_A1001`, press `D` to duplicate, `e` → `path` to change the ID to `inv_B2002` (or `e` → `header` to swap in account B's token), `r` to replay, `enter` to read the response. Two accounts, one object, one keystroke each — and because replays update the existing row rather than appending, your flow list stays a clean ledger of what you tried.

Intercept mode freezes a flow mid-flight so you can edit it *before* it reaches the server: `i` sets an intercept filter (e.g. `~u /checkout & ~q`), `I` toggles it on/off, `a` resumes the focused held flow, `A` resumes all, `X` kills one. Equivalent commands exist for scripting: `:replay.client @all`, `:replay.client "~d example.com"`, `:replay.server @focus`.

> **Discipline:** `r` sends exactly one request per press. That is the point — it keeps you at human speed and under any rate limit. Do not wrap it in a shell loop against a live target.

### Phase 3 — Addons as a repeater/intruder-lite

When the manual loop needs to run a handful of times with controlled variation, write a Python addon rather than reaching for a high-volume fuzzer. Addons are classes (or bare module-level functions) exposing event hooks; load with `-s`:

```
mitmdump -s myaddon.py --set opt=value
```

The hooks you will use most: `request(self, flow)` / `response(self, flow)` (full message read), `requestheaders` / `responseheaders` (headers only, body still streaming), `websocket_message(self, flow)`, and the lifecycle hooks `load(self, loader)` (register options), `configure(self, updated)`, `running(self)`, `done(self)`. Logging is Python-standard: `import logging; logging.info(...)` (the old `ctx.log` was removed in mitmproxy 9+).

**Example 1 — scoped header rewriter.** Injects or strips headers on in-scope flows only, driven by a mitmproxy filter so it never touches traffic outside your target:

```python
"""
header-rewriter.py — rewrite/inject headers on in-scope flows only.

    mitmdump -s header-rewriter.py \
             --set rewrite_scope="~d api.example.com" \
             --set inject_token="Bearer eyJhbGciOi...replace-me"
"""
import logging
from mitmproxy import ctx, flowfilter, http


class HeaderRewriter:
    def load(self, loader):
        loader.add_option(
            name="rewrite_scope", typespec=str, default="~d example.com",
            help="Only rewrite flows matching this mitmproxy filter.",
        )
        loader.add_option(
            name="inject_token", typespec=str, default="",
            help="Value to set as the Authorization header (empty = leave as-is).",
        )

    def configure(self, updated):
        if "rewrite_scope" in updated:
            self.flt = flowfilter.parse(ctx.options.rewrite_scope)
            if self.flt is None:
                raise ValueError("invalid rewrite_scope filter")

    def running(self):                      # compile once at startup too
        self.flt = flowfilter.parse(ctx.options.rewrite_scope)

    def request(self, flow: http.HTTPFlow) -> None:
        if not flowfilter.match(self.flt, flow):
            return                           # out of scope: untouched
        if ctx.options.inject_token:
            flow.request.headers["authorization"] = ctx.options.inject_token
        flow.request.headers.pop("x-debug-trace", None)   # strip a client leak
        logging.info(f"rewrote {flow.request.pretty_url}")


addons = [HeaderRewriter()]
```

This is how you test "does endpoint X trust a header the server should derive?" across a whole session — set `X-User-Id`, `X-Forwarded-For`, or a swapped bearer on every in-scope request at once, then watch which responses change.

**Example 2 — low-volume, rate-capped token fuzzer (intruder-lite).** A *command* you invoke against one focused flow. It replays a bounded candidate list through `replay.client`, with a hard count cap and an enforced delay — the opposite of a thousand-thread fuzzer:

```python
"""
token-fuzz.py — rate-capped token fuzzer. Focus a captured request, then:

    :fuzz.token @focus

Replays it at most fuzz_max times, one Authorization variant each,
sleeping fuzz_delay_ms between sends.
"""
import logging
import time
from mitmproxy import command, ctx, flow, http


class TokenFuzz:
    def load(self, loader):
        loader.add_option(
            name="fuzz_delay_ms", typespec=int, default=1000,
            help="Minimum milliseconds between replayed requests (rate cap).",
        )
        loader.add_option(
            name="fuzz_max", typespec=int, default=8,
            help="Hard cap on replayed requests per run.",
        )

    @command.command("fuzz.token")
    def fuzz_token(self, f: flow.Flow) -> None:
        if not isinstance(f, http.HTTPFlow):
            logging.warning("fuzz.token needs an HTTP flow")
            return
        current = f.request.headers.get("authorization", "")
        candidates = [
            "",                       # no token at all
            "Bearer null",
            "Bearer undefined",
            "Bearer " + "A" * 24,     # structurally-valid garbage
            current + "x",            # one-char-tampered signature
        ][: ctx.options.fuzz_max]

        delay = ctx.options.fuzz_delay_ms / 1000.0
        for i, cand in enumerate(candidates):
            dup = f.copy()
            if cand:
                dup.request.headers["authorization"] = cand
            else:
                dup.request.headers.pop("authorization", None)
            dup.comment = f"fuzz.token #{i}: {cand!r}"
            ctx.master.commands.call("replay.client", [dup])
            logging.info(f"replayed #{i} with {cand!r}")
            time.sleep(delay)         # blocks during the run — fine for <=8 sends


addons = [TokenFuzz()]
```

The `time.sleep` blocks mitmproxy for the duration of the run; for ≤8 requests at 1 s apart that is a feature, not a bug — it makes the rate cap unavoidable and keeps you inside any throttle. For real JWT attacks, export the focused request as `curl` (Phase 6) and hand the token to `jwt_tool`/`hashcat`; use this addon for the quick "which malformed token does the server accept?" sweep only. **Never raise `fuzz_max` into the hundreds or remove the delay against a live program target** — that is the line between a repeater and a denial-of-service.

### Phase 4 — Mobile and non-proxy-aware targets

The traffic that pays, because almost nobody else captures it.

**Local capture (per-process, desktop).** `--mode local:<process>` transparently intercepts one program with no proxy settings in the app at all: `mitmdump --mode local:curl`. Negate or list: `local:!firefox` (everything *but* Firefox), `local:curl,python3.11`, or by PID `local:42`. Linux requires sudo (it loads a BPF program), supports egress only, matches program names on the **first 16 characters**, needs Linux 6.8+, and inside a container needs `--network host`. This is the cleanest way to confine capture to the one process you are testing.

**Rooted device / emulator — Android system CA store.** Since Android 7, apps ignore user-added CAs, so the cert must go into the *system* store. Rename it to its subject hash and push it (emulator shown; a rooted device is the same once `/system` is writable):

```
cd ~/.mitmproxy/
hashed=$(openssl x509 -inform PEM -subject_hash_old -in mitmproxy-ca-cert.cer | head -1)
cp mitmproxy-ca-cert.cer "$hashed.0"                 # e.g. c8450d0d.0
emulator -avd Pixel_API_30 -writable-system          # must use -writable-system every boot
adb root && adb remount
adb push "$hashed.0" /system/etc/security/cacerts/
adb shell chmod 644 /system/etc/security/cacerts/"$hashed.0"
adb reboot
```

On Google-Play (production) emulator images `adb root` is blocked — install via **Magisk** instead: start `mitmweb`, boot the emulator with `-http-proxy 127.0.0.1:8080`, browse to `http://mitm.it/cert/magisk` on the device, and install `mitmproxy-magisk-module.zip` as a Magisk module. (After Magisk is installed you can no longer boot with `-writable-system` — it bootloops.) Verify under Settings → Security → Trusted credentials (the cert shows as `mitmproxy`).

**iOS.** Install via `mitm.it`, then the critical second step users forget: Settings → General → About → Certificate Trust Settings, and *enable full trust* for the mitmproxy root. Without it the cert is installed but not trusted for TLS.

**WireGuard mode** is the lowest-friction mobile option: `mitmweb --mode wireguard` runs a userspace WireGuard server (default `51820/udp`, keys in `~/.mitmproxy/wireguard.conf`); scan the QR from the WireGuard app and the device tunnels through mitmproxy with no root and no network-level redirect. **Transparent mode** suits a gateway setup where the test device uses the mitmproxy host as its default route:

```
sysctl -w net.ipv4.ip_forward=1
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80  -j REDIRECT --to-port 8080
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 443 -j REDIRECT --to-port 8080
mitmdump --mode transparent --showhost        # --showhost: display the Host-header URL
```

**Pinned apps** reject your CA outright (handshake fails, flows never appear). For an app you are authorized to repackage, unpin with `apk-mitm`, `objection`, `android-unpinner`, or `ssl-kill-switch2`. Treat unpinning as scope-sensitive: repackaging a binary you do not own can breach a program's rules — confirm it is permitted first. An app that *refuses* interception despite a trusted CA is itself worth a line in your notes: pinning is a control, and whether it is enforced consistently is a finding-adjacent observation.

### Phase 5 — Protocol surfaces: HTTP/2, WebSocket, mTLS, and response-side tests

**HTTP/2** is negotiated and decoded by default (the engine speaks HTTP/1, HTTP/2, and WebSockets; HTTP/3/QUIC is available via the `http3://`/`quic://` reverse schemes). The flow list shows each flow's protocol. To test how a backend handles a **protocol downgrade**, force HTTP/1.1 upstream with `--set http2=false` and compare — header-handling and smuggling-class differences sometimes surface only across versions.

**WebSocket** flows (filter `~websocket`) carry their messages in `flow.websocket.messages`; each has `.content`, `.from_client`, `.is_text`, and `.drop()`. WebSocket handlers are as prone to BOLA and missing per-message authz as HTTP routes, and almost nobody tests them. A message rewriter/observer:

```python
import logging
from mitmproxy import http

def websocket_message(flow: http.HTTPFlow):
    msg = flow.websocket.messages[-1]          # most recent frame
    who = "client" if msg.from_client else "server"
    logging.info(f"{who}: {msg.content!r}")
    # e.g. swap an object id in an outbound subscribe frame to test authz:
    if msg.from_client and b'"channel":"user:1001"' in msg.content:
        msg.content = msg.content.replace(b"user:1001", b"user:1002")
```

**Client certificates / mTLS targets.** When the *upstream* demands a client cert, supply yours so mitmproxy can complete the handshake on the client's behalf: `mitmdump --set client_certs=client.pem` (a single PEM with unencrypted key + cert, used for all connections) or `--set client_certs=./certdir/` (per-host: `example.org.pem` is used for `example.org`). This is exactly how you get *inside* an mTLS-protected API that would otherwise be a black box — once mitmproxy holds the client cert, every request is yours to inspect and replay. To make mitmproxy *request* a cert from the client instead, `--set request_client_cert=true` (off by default because some older Android clients abort on a `CertificateRequest`).

**Reverse-proxy mode for API debugging.** Point a client at mitmproxy and have it forward to a fixed upstream — no CA install on the client needed, because the client talks to *mitmproxy's* endpoint:

```
mitmdump -p 8443 --mode reverse:https://api.example.com@443
```

The scheme selects the protocol (`http://`, `https://`, `tcp://host:port`, `tls://`, `dns://`, `http3://`). The Host header is rewritten to the upstream by default; preserve the original with `--set keep_host_header=true`. This is the cleanest way to sit between a flaky SDK and a real API and watch (or rewrite) every exchange.

**map-local and sticky-cookie — client-side and session testing.**

- `--map-local '|example.com/app.js|~/poison/app.js'` serves a local file in place of a live response (`|url-regex|local-path`, or add a leading `|flow-filter` segment to scope it). Swapping in an instrumented JS bundle lets you probe DOM/client-side sinks without touching the server — the response substitution happens in your proxy. Its sibling `--map-remote '|//cdn.example.com/|//localhost:9000/'` rewrites the *destination* instead.
- `--set stickycookie='~d example.com'` makes mitmproxy re-attach the most recent server-set cookie to matching requests, so replayed and scripted requests keep a valid session without you pasting `Cookie:` headers each time. `stickyauth` does the same for `Authorization`/auth headers. Both keep the capture-to-replay loop usable across a session that would otherwise expire mid-test.
- Other response-side toggles: `--anticache` strips `If-None-Match`/`If-Modified-Since` so you always get full responses (not 304s); `--modify-headers '/~q/Host/example.org'` rewrites a header on matching flows (empty value removes it); `--modify-body '/~q/foo/bar'` rewrites bodies by regex.

---

## Where the money is with mitmproxy

mitmproxy enables finding classes rather than detecting them; the table maps each class to the capability that reaches it. "Dupe risk" is how crowded that surface typically is on a public program.

| Finding class | mitmproxy capability that reaches it | Typical severity | Dupe risk |
|---|---|---|---|
| BOLA / IDOR | capture-to-replay loop (`D` → `e` id/token → `r`), or two-token header-rewriter addon | High–Critical | Medium |
| Broken auth / weak or forgeable tokens | `token-fuzz` addon for the acceptance sweep; export-to-`curl` → `jwt_tool`/`hashcat` | High–Critical | Medium |
| Mass assignment | intercept request (`i`) → `e` body → add privileged field → `r` | Critical | Medium |
| Hidden endpoints / API inventory (API9) | passive capture of **mobile/native** traffic (`local`, WireGuard, transparent) | Medium–High | **Low** |
| Sensitive data over the wire / secrets in traffic | passive `~bs` / `~t json` inspection; HAR as evidence | Medium–High | Low |
| WebSocket authz / per-message BOLA | `websocket_message` addon, `~websocket` filter | High | **Low** |
| Client-side injection / DOM sinks | `--map-local` to serve instrumented JS under the real origin | Medium–High | Low |
| Response manipulation / client-trust bugs | intercept response → `e`, or `--map-local`/`--modify-body` | Medium–High | Low |
| mTLS-gated API bugs (any class, behind a client cert) | `--set client_certs=` to get inside, then the normal loop | inherits the class | **Low** |
| Cert pinning / plaintext-on-wire (hardening) | observing which flows refuse the CA; plaintext segments in capture | Low–Medium | High |
| CORS / Host / proxy-header trust | header diffing across captured flows | Low–Medium | High |

The three low-dupe rows — mobile API inventory, WebSocket authz, and anything behind mTLS — are mitmproxy's edge. They are under-tested *because* they need exactly this tool.

---

## Command recipes

Annotated, low-volume by construction. Every destructive or high-volume setting is called out.

```
# Launch with a per-engagement config dir and a decryption fence
mitmweb --set confdir=~/engagements/acme/.mitmproxy \
        --ignore-hosts '^(?!.*\.example\.com:443$)'
# -> only *.example.com:443 is decrypted; all other TLS is tunnelled untouched.
#    Keeps out-of-scope hosts and personal traffic out of your capture.
```

```
# Non-interactive capture of one process to a flow file, filtered to the target
mitmdump --mode local:myapp -w acme-baseline.flows "~d example.com"
# -> trailing "~d example.com" filters what is saved; -w streams flows to disk.
#    This is your evidence baseline and your diff reference.
```

```
# Re-open a saved capture offline (no proxy server) to review or re-export
mitmproxy -n -r acme-baseline.flows
# -> -n starts no listener; -r reads the file. Safe to do on the plane.
```

```
# Replay a single captured request after editing it, from the console
#   focus the flow, then:
:replay.client @focus           # one request, one time
#   or target a set explicitly (still bounded, still your choice):
:replay.client "~u /api/v2/invoices & ~m GET"
```

```
# Save evidence: one flow, or the currently-shown set (NOT @all unless needed)
:save.file @focus acme-bola-poc.flows       # + prefix on path = append
# Portable evidence a triager can replay in browser devtools:
:save.har @all acme-session.har             # HAR of all flows; .zhar = compressed
```

```
# Export the exact PoC request as copy-pasteable curl
:export.file curl @focus poc.sh             # formats: curl httpie raw raw_request raw_response
:export.clip curl @focus                    # straight to clipboard
```

```
# Serve an instrumented JS bundle in place of the live one (client-side testing)
mitmdump --map-local '|example.com/static/app.js|~/poison/app.js'
# -> response substitution happens in the proxy; the server is never sent a payload.
```

```
# Keep a session alive across scripted replays without pasting Cookie headers
mitmdump --set stickycookie='~d example.com'
```

```
# Debug an API by sitting in front of it (no CA install on the client)
mitmdump -p 8443 --mode reverse:https://api.example.com@443
# -> point the client at https://localhost:8443; watch/rewrite every exchange.
```

```
# Get inside an mTLS-protected upstream
mitmdump --set client_certs=~/engagements/acme/client.pem \
         --mode reverse:https://mtls.example.com
# -> single PEM (unencrypted key + cert) used for all upstream TLS.
```

```
# Dump a HAR on exit for a whole mitmdump run (note the memory caveat below)
mitmdump --set hardump=acme-session.har "~d example.com"
# -> writes HAR when the process exits; keeps all flows in memory until then.
```

Rate/volume guards worth making habits: the `r` key and `:replay.client` send **one** request per invocation — never shell-loop them against a live target; the `token-fuzz` addon above ships with `fuzz_max=8` and a 1 s `fuzz_delay_ms` — leave them low; and `hardump` holding every flow in memory means a long capture can OOM — prefer streaming to `.flows` with `-w` for long runs and export HAR from the file afterward (`mitmdump -n -r run.flows --set hardump=run.har`).

---

## Pitfalls, noise, and scope discipline

How hunters get reports rejected — or get banned, or break the law — with this tool, and the settings that prevent it.

- **Capturing out-of-scope or personal traffic.** `--mode local` (whole machine), transparent, and WireGuard capture *everything* the device emits — other apps, OS telemetry, the victim's banking app if it is a personal phone. Decrypting traffic you are not authorized to decrypt is the cardinal sin. Fence it with `ignore_hosts` (decrypt only in-scope hosts), `--mode local:<oneprocess>`, and a **dedicated test device/emulator or a throwaway profile** — never your daily driver.
- **MITMing a personal device's whole TLS.** Installing your CA into a phone's system store makes mitmproxy able to read *all* that device's secure traffic. Use a wiped test device or emulator; remove the CA afterward.
- **Turning a repeater into a DoS.** Removing the delay or raising the cap on the fuzzer addon, shell-looping `replay.client`, or pointing `--map-remote` at a high-rate generator can trip rate limits (poisoning your account) or exhaust the target. Keep replays single and manual; keep `fuzz_max` and `fuzz_delay_ms` conservative; most programs forbid automated high-volume testing outright.
- **Forgetting intercept is on.** Leaving `i`/`I` active silently holds or drops real traffic — you will think the app is broken, or you will mangle a legitimate request. Clear the intercept filter when you step away from a flow.
- **`hardump` OOM on long captures.** The option deliberately keeps all flows in memory to write HAR on exit; a multi-hour session can run the box out of RAM and lose everything. Stream to `.flows` with `-w` and convert offline.
- **Accidental writes to production.** `--modify-body`, `--map-remote`, and an over-broad header-rewriter can inject test payloads into real requests, or send a mutated body to a state-changing endpoint. Scope every modify/map rule with a tight flow-filter segment and test it against a captured flow before going live.
- **Reverse mode pointing out of scope.** `--mode reverse:` forwards to whatever upstream you name; double-check the host is in scope before you relay a client's real session through it.
- **Unpinning apps you don't own.** Repackaging a production APK/IPA to defeat pinning can violate a program's rules of engagement even when the endpoints are in scope. Confirm it is permitted; if not, an app that cleanly refuses interception is simply out of reach by that route.
- **Submitting raw captures full of other people's data.** A `.flows` or `.har` file from a shared or multi-user environment can contain unrelated PII, session tokens, and credentials. Trim to the minimal flows, redact, and never attach a full session dump.

---

## Reporting evidence

mitmproxy produces unusually clean PoCs because it captures the *exact bytes on the wire*, not a tool's reconstruction. For each finding, capture:

1. **The minimal flow set — not the whole session.** Save only the proving flows: `:save.file @focus finding.flows` (or `@shown` after filtering). A native `.flows` file preserves request, response, headers, bodies, and timing exactly, and a triager with mitmproxy can replay it — but it is mitmproxy-specific, so also provide a portable form.
2. **A copy-pasteable PoC request.** `:export.file curl @focus poc.sh` turns the focused request into a `curl` one-liner the triager can run as-is; `export` also offers `httpie`, `raw`, `raw_request`, and `raw_response`. This is the single most useful artifact in the report.
3. **A HAR for browser-native replay.** `:save.har @all finding.har` (or `--set hardump=`) writes a HAR the triager can import into browser devtools or Burp; `.zhar` compresses it. HAR has been first-class since mitmproxy 10.1.0 (both `-r file.har` to read and `save.har`/`hardump` to write).
4. **The before/after pair.** For BOLA/mass-assignment, show the baseline (your own object/fields) *and* the exploited flow side by side — the duplicate-then-edit loop gives you both rows naturally. Include status code, the telling response fields, and timing where it matters (races, ReDoS).
5. **Reproduction context.** The mode you used (`reverse:`, `local:`, WireGuard), whether a client cert was needed, and the CA-install steps for a mobile target — so the triager can stand the setup back up.

Redaction is mandatory: strip other users' data to the minimum the proof needs, rotate any real token you captured, and never exfiltrate more than the single object/record that demonstrates impact. Severity framing follows the underlying class (BOLA cross-tenant read → High; authenticated state change or privilege escalation → Critical; plaintext-on-wire where TLS terminates upstream → a deployment hardening note, not an app bug) — mitmproxy is how you *prove* it, not what sets the rating.

---

## References

- mitmproxy — proxy modes — https://docs.mitmproxy.org/stable/concepts/modes/
- mitmproxy — certificates (CA, `--certs`, `confdir`, `client_certs`, `request_client_cert`, `upstream_cert`, pinning) — https://docs.mitmproxy.org/stable/concepts/certificates/
- mitmproxy — filter expressions — https://docs.mitmproxy.org/stable/concepts/filters/
- mitmproxy — commands and the `:` prompt — https://docs.mitmproxy.org/stable/concepts/commands/
- mitmproxy — getting started / the three tools — https://docs.mitmproxy.org/stable/overview/getting-started/
- mitmproxy — features (map-local/remote, modify-headers/body, stickycookie, anticache, replay, streaming) — https://docs.mitmproxy.org/stable/overview/features/
- mitmproxy — addons overview (`-s`, `addons = [...]`, live reload) — https://docs.mitmproxy.org/stable/addons/overview/
- mitmproxy — event hooks reference — https://docs.mitmproxy.org/stable/api/events.html
- mitmproxy — example addons — https://docs.mitmproxy.org/stable/addons/examples/
- mitmproxy — HTTP API (`HTTPFlow`, `Request`/`Response`, `copy()`, `get_text`, `query`) — https://docs.mitmproxy.org/stable/api/mitmproxy/http.html
- mitmproxy — addon manager / `Loader.add_option` — https://docs.mitmproxy.org/stable/api/mitmproxy/addonmanager.html
- mitmproxy — transparent proxying (iptables, `--showhost`) — https://docs.mitmproxy.org/stable/howto/transparent/
- mitmproxy — install system CA on Android (subject-hash rename, system store, Magisk) — https://docs.mitmproxy.org/stable/howto/install-system-trusted-ca-android/
- mitmproxy source — console default keymap — https://github.com/mitmproxy/mitmproxy/blob/main/mitmproxy/tools/console/defaultkeys.py
- mitmproxy source — `savehar` addon (`hardump`, `save.har`, `.zhar`) — https://github.com/mitmproxy/mitmproxy/blob/main/mitmproxy/addons/savehar.py
- mitmproxy source — `save` addon (`save.file`, `+` append) — https://github.com/mitmproxy/mitmproxy/blob/main/mitmproxy/addons/save.py
- mitmproxy source — `export` addon (curl/httpie/raw formats) — https://github.com/mitmproxy/mitmproxy/blob/main/mitmproxy/addons/export.py
- mitmproxy source — `cmdline.py` (`-r`/`-w`/`-n`/`-s`/`-p`/`-C`/`-S`/`--set`/filter positional) — https://github.com/mitmproxy/mitmproxy/blob/main/mitmproxy/tools/cmdline.py
- mitmproxy — CHANGELOG (HAR added 10.1.0, 2023-09-24; current 12.3.1, 2026-10-25) — https://github.com/mitmproxy/mitmproxy/blob/main/CHANGELOG.md

*Research supplement added 2026-11-15, grounded in the mitmproxy 12.3.1 official documentation and source tree (docs.mitmproxy.org/stable and github.com/mitmproxy/mitmproxy — modes, certificates, filters, addon event API, console keymap, savehar/save/export addons, cmdline, and CHANGELOG, all consulted 2026-11-15) (model: claude-opus-4-8).*
