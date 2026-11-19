---
technology: "AJP (Apache JServ Protocol)"
kind: overview
audience: offensive-security
family: web-platforms
first-released: 1997 (JServ era; AJP13 in 2000)
license: Apache-2.0
spec: "https://tomcat.apache.org/connectors-doc/ajp/ajpv13a.html"
provenance: "upstream-grounded (corpus evidence: 1 writeup with a bare 8009 listener; other marker hits were hex collisions); facts verified against Tomcat/RedHat/Debian advisories 2026-11-12"
---

# AJP (Apache JServ Protocol)

> A binary proxy protocol that was never meant to face the internet — it trusts the peer to set servlet-internal request attributes, so anyone who can speak it raw talks to the servlet engine as if they were the trusted webserver.

## What it is

AJP13 is the wire protocol Apache httpd (via mod_jk or mod_proxy_ajp) uses to forward requests to a servlet container — overwhelmingly Tomcat, port 8009. It exists to move HTTP between webserver and app server cheaply: a compact binary format (packets prefixed `0x1234` client→server, `0x4142` server→client) that serializes method, headers, and — crucially — **request attributes**. Attributes are the attacker's lever: the servlet container trusts that anything arriving over AJP came from the sanctioned proxy, and consumes attributes like `javax.servlet.include.request_uri` / `path_info` / `servlet_path` as internal dispatch directives no HTTP client should ever control.

That trust model breaking is **Ghostcat (CVE-2020-1938)**: by speaking AJP directly to an exposed connector, an unauthenticated attacker injects include-attributes and gets arbitrary file read/inclusion from the web application's root (`/WEB-INF/web.xml` and friends — configuration, database credentials, servlet source paths), escalating to RCE where the app deposits uploads inside its own webapp. Fixed in Tomcat 9.0.31 / 8.5.51 / 7.0.100 (Feb 2020); the fix also hardened the connector defaults (required secret, address expectations).

Two context facts sharpen what an exposed 8009 means: on the affected-era Tomcat, the AJP connector was **defined and active in the default `server.xml`** — installs that never touched it still listened on 8009, often on all interfaces. And Debian-family packages have shipped AJP **disabled in their default config since 2008** — so on a Debian-derived host, an open 8009 proves someone deliberately enabled it.

## The anatomy that matters on an engagement

    attacker ──(raw AJP: binary FORWARD packet + injected attributes)──> :8009
    intended path:  httpd (mod_jk / mod_proxy_ajp) ──AJP──> Tomcat connector
    connector:  parses packet → attributes land in Request internals
                → include-attributes dispatch to files the HTTP layer would never serve
                → response serialized back (0x4142)

Three properties drive the findings:

- **No TLS, no auth, no rate limiting** — the connector assumes the peer is the trusted front-end; AJP-bound vhosts never see the internet by design, so nothing defends it.
- **Attributes are trusted servlet dispatch directives** — the include-family turns a protocol quirk into file disclosure (Ghostcat), and other attributes (`ssl_*`, `remote_user`, `jk_*`) propagate identity data the app may treat as authoritative.
- **The webapp root is the read boundary** — disclosure is confined to the app's directory tree, but that tree holds `web.xml`, `WEB-INF/classes/`, bundled configs, and sometimes uploaded content — exactly the files that unlock the next step.

## Where you'll meet it

- **Forgotten 8009 on internet-facing Tomcat** — the classic: default `server.xml` with AJP defined, box exposed whole. Version ≤ 8.5.50/9.0.30 = Ghostcat directly.
- **Behind a reverse proxy** — architectures where httpd fronts Tomcat over AJP; the interesting bugs become smuggling/desync pairs across the proxy boundary (AJP's hop-by-hop assumptions differ from HTTP/1.1's), plus attribute-trust spoofing when the app consumes `remote_user`/TLS attributes.
- **Internal network pivots** — 8009 reachable from a foothold inside the DMZ; same attacks, no edge filtering.

## Reading the connector's mind

1. **`8009` answering with a `0x4142`-shaped binary response to a FORWARD packet is conclusive** — nmap's `ajp-*` NSE scripts do this handshake for you.
2. **Ghostcat gates on version**: affected 9.0.0.M1–9.0.30 / 8.5.0–8.5.50 / 7.0.0–7.0.99; fixed 9.0.31 / 8.5.51 / 7.0.100.
3. **File read is the default outcome, RCE is conditional** — inclusion of an attacker-uploaded file inside the webapp root executes code only when the app (or a sibling feature) lets you place one there.
4. **On Debian-family Tomcat, AJP is off by default** — an open 8009 there means an explicit configuration decision, and often an old custom `server.xml` (i.e., an unpatched version too).
5. **The proxy in front may be the weaker half** — when httpd → AJP fronts the internet, test the pair for request smuggling rather than only probing 8009 directly.

## Where to go next

[[ajp-bug-bounty|AJP bug bounty methodology]] — connector detection and fingerprinting, the Ghostcat file-read with parsing recipes for `web.xml`, attribute-trust abuse, and proxy-pair smuggling checks.
