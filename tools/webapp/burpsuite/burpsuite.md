---
name: "burpsuite"
categories:
  - blackarch-webapp
version: "2026.10"
homepage: "https://portswigger.net/burp/releases"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
worth: 1.5
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# burpsuite

> PortSwigger Burp Suite (Community Edition) — an intercepting web proxy plus manual testing tools for attacking web applications.

## What it's for

Burp Suite is a graphical Java workbench for web security testing: it sits between your browser and the target as an intercepting proxy, then lets you inspect, modify, replay and attack every HTTP/WebSocket message. It is the standard tool for manual web testing — request tampering, parameter fuzzing, encoding/decoding, session-token swapping — and other tools (sqlmap, curl) can be chained through its listener. The Community Edition jar packaged here covers the manual toolkit (Proxy, Repeater, Intruder, Decoder); the automated vulnerability scanner is a paid Professional feature, so it does not find bugs for you. Burp's own outgoing traffic can also be tunneled through an upstream/SOCKS proxy (Settings → Network → Connections), e.g. to reach internal hosts via an SSH tunnel. As a GUI application it is driven from its windows; the command line only starts it.

## Typical use cases

### Launch the Burp Suite jar

Start the GUI; the packaged `burpsuite` wrapper just runs the jar with Java.

```
burpsuite
# equivalent:
java -jar /usr/share/burpsuite/burpsuite.jar
# persistent project + configuration file:
java -jar /usr/share/burpsuite/burpsuite.jar --project-file=proj.burp --config-file=proj.json
```

Outcome: the startup wizard opens (temporary or disk project) and the proxy listener comes up on 127.0.0.1:8080; with `--project-file` the HTTP history and state survive restarts. Other launcher flags: `--user-config-file`, `--disable-extensions`, `--diagnostics`, `--use-defaults`.

### Point a browser at the proxy and trust its CA

Route a browser through the listener and import Burp's CA certificate so HTTPS can be intercepted.

```
# browser proxy settings: HTTP/HTTPS proxy 127.0.0.1 port 8080
curl http://127.0.0.1:8080/cert -o burp.der    # with Burp running
```

Outcome: `burp.der` holds Burp's CA certificate (generated on first launch); import it into the browser/OS trust store and HTTPS pages load without warnings while proxied. Burp's bundled browser (Proxy → Intercept → Open Browser) is preconfigured and needs no certificate step.

### Intercept and inspect traffic in the Proxy

Hold and examine live requests.

```
Proxy > Intercept: "Intercept is on" -> browse http://target.example.com
  edit a held request, then Forward (or Drop)
Proxy > HTTP history: review every request/response pair, filterable
```

Outcome: with interception on, each browser request queues in Burp for editing before release; the HTTP history keeps a full transcript even with interception off, which is usually enough to understand an endpoint without intercepting at all.

### Manipulate and replay a request in Repeater

Send a captured request to Repeater, edit it, resend as often as needed.

```
right-click a request (HTTP history / Intercept) -> Send to Repeater (Ctrl+R)
Repeater tab: change method, path, headers, body -> Send
  e.g. id=124 -> id=36        # test access to other objects
  e.g. append role=admin      # try mass-assignment style parameters
```

Outcome: the request sits in its own numbered Repeater tab; every Send shows the fresh response and the history arrows step back through prior pairs. Different responses for other IDs (or an accepted privileged parameter) indicate broken access control.

### Tamper with a file upload

Intercept an upload and change its filename or content type.

```
Proxy intercept on -> perform the upload -> edit the held request:
  multipart filename="avatar.png" -> filename="avatar.php"
  Content-Type: image/png -> application/pdf
```

Outcome: the modified multipart request reaches the server; an uploaded file that is still stored, parsed or executed under the new name/type shows the validation is only client-side or content-type-based.

### Fuzz with Intruder

Automate many variations of one request.

```
right-click request -> Send to Intruder
Intruder > Positions: set payload positions (Add § / Clear § / Auto §)
Intruder > Payloads: assign payload set(s) -> Start attack
```

Outcome: Burp fires one request per payload at each § position; the attack window lists status/length/time per request so anomalous rows stand out — e.g. cycling an ID parameter through every object to enumerate access.

### Encode and decode payloads

Convert payload text without leaving Burp.

```
message editor: select text -> Ctrl+U          # URL-encode the selection
Decoder: paste value -> Decode as... base64    # analyze opaque values
```

Outcome: the selection is URL-encoded in place (ready to inject into a parameter), and Decoder renders each transformation as a stacked view — e.g. base64-decoding an opaque cookie or JWT-like token found in the HTTP history.

### Capture and replay session tokens

Keep an authenticated session while testing.

```
HTTP history / Repeater response: copy the Set-Cookie or token value
Repeater: replace the Cookie: / auth-token header with the captured value -> Send
```

Outcome: your crafted requests continue under the captured session; when it expires, re-authenticate in the browser and swap the fresh cookie into Repeater without rebuilding the request.

### Automate edits with match-and-replace rules

Apply the same edit to every passing request.

```
Proxy > Settings -> Match and replace rules -> Add:
  type: request header or body, match (regex), replace -> save
```

Outcome: every subsequent request through the proxy gets the substitution applied automatically (e.g. rewriting a header or parameter on all traffic) instead of editing each request by hand.

### Export a request for another tool

Hand a captured request to sqlmap or route a tool through Burp.

```
# from Burp: right-click the request in HTTP history -> save it to req.txt
sqlmap -r req.txt --batch
# or send another tool's traffic through Burp's listener:
sqlmap -u "http://target.example.com/item?id=1" --proxy=http://127.0.0.1:8080
```

Outcome: sqlmap replays the exact captured request (cookies and headers included), or its probes appear in Burp's HTTP history for inspection while the attack runs.
