---
technology: "Squid"
kind: overview
audience: offensive-security
family: network-services
layer: HTTP forward/caching proxy (3128 default; ACLs, auth helpers, cache manager, WPAD/PAC serving)
language: C++
first-released: 1996 (Harvest/Squid lineage)
license: GPLv2
homepage: "http://www.squid-cache.org"
github-repo: "squid-cache/squid"
evidence: "5 files (command-context gate: squid.conf/cachemgr_passwd/cache_object references, squid/N.M banners, 3128/tcp + http-proxy|squid-http nmap service lines, curl --proxy ... :3128 invocations, /usr/sbin/squid paths; the parent http-proxy placeholder's 15 was an area count including 23-file proxychains attacker-tooling and word collisions — area probe documented in ../internal coverage ledger)"
status: enriched
provenance: "mined from 5 evidence files + upstream-verified: squid-cache.org cache manager feature pages (/squid-internal-mgr path, Basic auth semantics, cache_object scheme), squid-cache.org cachemgr_passwd directive docs (action list, disable/none/all keywords), accessed 2026-11-12. Supplement (2026-11-13) rewritten from HackTricks (src/network-services-pentesting/3128-pentesting-squid.md), CC BY-NC 4.0, accessed 2026-11-13 (egress validation + egress-IP reveal, cloud-metadata and IPv6/TLS-CONNECT destination classes, spose/mysquidscanner scanners, megamansec smuggling/cache-poisoning audit class — see the methodology supplement)."
---

# Squid

> A proxy whose access control is a config file written in prose ("allow localhost", "deny !Safe_ports") — read the ACLs and you have a map of exactly which internal hosts you can now touch with `curl --proxy`.

## What it is

Squid (squid-cache.org, C++, GPLv2, 1996 Harvest lineage) is the canonical open-source HTTP forward proxy and cache, listening on 3128 by default. Clients send it ordinary HTTP requests (or CONNECT for tunnels); `squid.conf` decides with `http_access` rules who may ask and what may be fetched, optionally demanding authentication (`auth_param` helpers, Basic/Digest/Negotiate) surfaced to clients as `Proxy-Authenticate: 407` with a `Proxy-Authorization` header coming back. Squid deployments appear as explicit egress filters, as chained proxies (a `cache_peer` hierarchy), and — relevant to attackers — as the WPAD/PAC infrastructure host: the machine serving `wpad.dat` that tells every browser in the domain where the proxy is.

The operator's mental model: **Squid is an oracle for internal network structure that also happens to forward your requests there**. Three things leak: the *response* — a 403 `X-Squid-Error` page names the ACL that blocked you and often internal hostnames; the *cache* — the cache manager interface at `/squid-internal-mgr/` exposes DNS cache (`fqdncache`), peer lists and server lists, frequently with a password sitting in `squid.conf` itself (`cachemgr_passwd`); and the *config* — `squid.conf` in a backup, a config-management repo, or a mounted volume names every internal vhost and holds the proxy password. The evidence base hit all three.

The takeaway: on an external assessment, a `3128/tcp open http-proxy Squid` line is not "just a proxy" — it is often the only externally reachable service, and the entire box falls to enumeration *through* it (internal vhosts, loopback-only web servers, adjacent hosts) followed by exploitation of whatever you find there.

## The anatomy that matters on an engagement

    nmap: 3128/tcp open http-proxy Squid http proxy 4.11  (+ X-Squid / squid/N.M error headers)
      → test as client: curl --proxy http://TARGET:3128 http://example.com/   (open? 403 ACL? 407 auth?)
      → creds: 407 → leak hunt (squid.conf in backups/rsync/git; proxy user DB; other-box reuse)
      → through-proxy enum: internal vhosts, 127.0.0.1:80, RFC1918/172.16/12 ranges, adjacent :3128s
      → cache manager: /squid-internal-mgr/<action> with Basic auth (password from squid.conf)
        → fqdncache/server_list = internal host inventory → target the unpatched backend
      → interactive: proxychains chaining, Burp upstream proxy → browser-grade testing through squid

Three structural properties drive most findings:

1. **The ACL policy is the attack surface.** `http_access allow localhost` (a common default) means a request whose source appears to be the proxy itself — reachable via a chained loop — bypasses client auth entirely (observed: a `proxy → 127.0.0.1:3128` chain). ACLs also *enumerate*: each denial names the rule, and the set of reachable destinations sketches the internal network.
2. **The cache manager is an internal inventory oracle.** `/squid-internal-mgr/` serves management actions over plain HTTP with Basic auth; when `cachemgr_passwd <password> <actions...>` exists in `squid.conf`, that same password file you leaked unlocks `fqdncache` (resolved internal hostnames + IPs) and `server_list` — a map of the hidden network, no scanning needed (observed).
3. **Proxy credentials are enterprise credentials.** The 407 password is stored somewhere readable (config, credential DB, another host's backup) and is reused; and once you hold it, response headers from load-balanced backends (`Intranet-Host:` style) enumerate the server fleet behind the proxy (observed: creds leaked from a Squid passwd DB; headers exposed the pool members).

## Where you'll meet it

- **Perimeter box with only 3128 exposed** — the proxy is the whole engagement until you pivot: enumerate internal vhosts and loopback services through it, then exploit what you reach (observed repeatedly).
- **Internal squid in a proxy chain** — enterprises chain squids for egress; each hop is a new ACL and a new `cache_peer` inventory, and the localhost/127.0.0.1 hop often skips authentication (observed: a double-hop into a Kerberos-heavy network).
- **Config management and backups** — `squid.conf` travels in rsync modules, encrypted backups, and config repos; it holds ACLs, internal hostnames, `cachemgr_passwd`, and sometimes client credentials in cleartext (observed repeatedly).
- **WPAD/PAC infrastructure** — the `wpad.<domain>` host serving `wpad.dat` is frequently the proxy itself; discover it via DNS, fetch the PAC through the proxy it advertises, and you have the routing policy for every domain-joined browser (observed).

## Reading the framework's mind

1. **`3128` answering with an `X-Squid-Error`/`squid/N.M` flavored response is conclusive** — nmap prints `http-proxy Squid http proxy 4.11`; the error pages carry the version and the proxy's hostname (`Generated ... by srv01.example.com (squid/4.11)`).
2. **A 403 tells you what the ACL permits in negative** — read the denial text for rule names and hostnames, then try loopback (`http://127.0.0.1/`), RFC1918 targets, and the proxy's own internal vhosts; "Invalid URL" vs "Access Denied" vs 407 distinguishes no-auth-allowed, blocked-destination, and password-required states (observed: exactly these distinctions).
3. **Credentials ride the URL userinfo or the header** — `curl --proxy http://user:pass@TARGET:3128/` (URL-encode specials like `<`, `>`, `^` in passwords or curl/gobuster will misparse) or `Proxy-Authorization: Basic base64(user:pass)`; the same pair works for cache-manager Basic auth where the *password* is checked against `cachemgr_passwd` and the username is ignored (upstream wiki).
4. **The cache manager actions worth knowing by name** — `menu` (list everything), `fqdncache`/`ipcache` (DNS cache = internal host inventory), `server_list`/`peer_select` (upstream/peer topology), `config` (the effective config!), `shutdown*`/`reconfigure*` (destructive, password-gated). Until Squid 6.5 the `cache_object://` URL scheme worked too (upstream wiki).
5. **Everything you do through the proxy arrives as the proxy's IP** — the webapps behind it trust `X-Forwarded-For`-style headers or nothing at all; loopback-bound admin panels and "internal-only" vhosts are the canonical kills (observed: the default nginx page on `127.0.0.1:80` reached through the proxy).

## Where to go next

[[squid-bug-bounty|Squid bug bounty methodology]] — fingerprinting the proxy state, credential-acquisition routes, through-proxy enumeration workflows, cache-manager abuse, and chaining tooling (proxychains, Burp upstream proxies).
