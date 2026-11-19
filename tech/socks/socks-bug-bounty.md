---
technology: "SOCKS proxy (SOCKS4/5)"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
layer: "Circuit-level relay (RFC 1928/1929; TCP CONNECT/BIND, UDP ASSOCIATE; default port 1080)"
status: "upstream-only — bucket-2 new-unit, zero corpus evidence by design"
ht-source:
  - "rewritten from HackTricks (src/network-services-pentesting/1080-pentesting-socks.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only: rewritten from HackTricks per the third-party-reference rule; NSE script names/arguments verified against nmap.org nsedoc (socks-brute, socks-open-proxy), handshake bytes against RFC 1928/1929, and the 3proxy configuration shape against the 3proxy upstream README plus the tools vault 3proxy note. Dante treated at banner level only — its current upstream documentation pages were not retrievable this pass, so no Dante-specific defaults are asserted. Zero corpus evidence by design (new-unit wave 3, 2026-11-13)."
---

# SOCKS (1080) — enumeration and pivoting

Target model: a SOCKS4/5 listener, default port 1080/tcp. SOCKS relays a
client's connection to a destination through the proxy: method
negotiation first (RFC 1928), then TCP `CONNECT`, `BIND`, or UDP
ASSOCIATE, with RFC 1929 username/password as an optional sub-negotiation.
Two facts drive every finding here: **authentication is optional in the
protocol**, and **the proxy chooses which destinations it will reach** —
so the two questions for any listener are "does it want credentials?"
and "will it connect inward?" A SOCKS listener that answers both
wrong-way is a full network pivot, the exact property that makes SOCKS
the standard post-exploitation relay (and why a SOCKS daemon found on a
host you did not install it on is itself an incident indicator —
red-team tooling and commodity malware both drop them). Compare
[[turn-bug-bounty|TURN relay]] (the WebRTC world's
authenticated equivalent) and [[http-proxy]]/[[squid]] for the
HTTP-layer version of the same pivot.

`socks5h` / `--socks5-hostname` is a client convention worth internalizing:
it asks the proxy to resolve the hostname remotely. Plain `socks5`
resolves locally — which leaks your query to the local resolver and can
split behavior (internal names only resolving on the proxy side).

## Phase 1 — Method enumeration (is it open?)

The three-byte raw handshake is the ground truth; one byte of method
offered, no-auth:

```bash
printf '\x05\x01\x00' | nc -nv 198.51.100.10 1080
```

Reply meanings per RFC 1928: `\x05\x00` = NO AUTHENTICATION REQUIRED
(open relay unless destination rules stop you); `\x05\x02` =
username/password required (RFC 1929 sub-negotiation follows);
`\x05\xff` = no acceptable methods for what you offered (retry offering
more methods before concluding anything).

Nmap packages the same questions:

```bash
nmap -p 1080 -sV 198.51.100.10
nmap --script socks-open-proxy -p 1080 198.51.100.10
```

`socks-open-proxy` (categories default/safe) attempts socks4 **and**
socks5 payloads and reports an open proxy when it receives a
Request-Granted response — by default it tries to reach
`www.google.com:80`; override with `--script-args proxy.url=<host>,proxy.pattern=<pattern>`
so the verdict reflects *your* egress/segmentation question rather than
general internet reachability. A socks4-open/socks5-closed split is
common on old daemons and worth recording for the report.

## Phase 2 — Credential attacks (when Phase 1 said `\x05\x02`)

```bash
nmap --script socks-brute -p 1080 198.51.100.10
nmap --script socks-brute --script-args userdb=users.txt,passdb=rockyou.txt,unpwdb.timelimit=30m -p 1080 198.51.100.10
hydra -L users.txt -P passwords.txt -s 1080 -t 16 198.51.100.10 socks5
```

`socks-brute` takes the standard unpwdb/brute library arguments (userdb,
passdb, timelimit, threads, delays) and reports valid pairs; hydra's
socks5 module does the same with its own pacing. These are online
attacks against a real listener — keep concurrency conservative, watch
for lockout/alerting, and prefer a short targeted list over rockyou
unless the engagement explicitly covers heavy guessing.

## Phase 3 — Validate and pivot

Egress first (does the proxy reach the internet at all?), then inward
(the finding):

```bash
curl --socks5-hostname 198.51.100.10:1080 https://ifconfig.me
curl --socks5-hostname user:pass@198.51.100.10:1080 http://internal-target.example.com
proxychains4 -q nmap -sT -Pn --top-ports 200 10.0.0.x
```

Full TCP-connect scanning through proxychains (`-sT` only; SYN scans
cannot traverse a circuit proxy) or simply pointing every TCP tool you
own at the SOCKS listener via its proxy settings. The proxy's vantage
point is the target's inside position: enumerate the ranges reachable
*from the proxy host*, not just single hosts.

## Phase 4 — Know the implementations you will meet

- **3proxy** — the tiny multi-protocol relay (HTTP/HTTPS CONNECT, SOCKS4/5,
  portmapping, SNI proxy in one binary, plain-text config). A minimal
  config that turns any host into a dual listener is literally:
  ```text
  log
  nserver 8.8.8.8
  nscache 65536
  proxy -p3128
  socks -p1080
  ```
  With no `auth`/`users` directives above it, those listeners are
  unauthenticated relays — on a foothold box that is exactly what an
  attacker wants, and on an internet-facing host it is an open proxy
  finding. The same config format supports per-user credentials and
  traffic/bandwidth limits, so 3proxy in the wild ranges from locked
  down to wide open; check the cfg, not the banner.
- **Dante** (danted) — the classic full-featured Unix SOCKS server.
  Banner says Dante; the questions remain Phase 1/Phase 3 (which methods
  accepted, which destinations reachable). Its configuration lives in
  `danted.conf`, where auth-method and rule sections decide everything —
  obtain and read it on any host you land.
- **MikroTik RouterOS** — SOHO/ISP routers exposing a SOCKS interface;
  default-off but frequently enabled for "troubleshooting" and forgotten.
  The banner string identifies it; treat an enabled, unauthenticated
  router SOCKS as high-severity routing infrastructure compromise
  adjacent (arbitrary egress through the edge device).
- Banner strings from internet-wide scans (`3proxy`, `Dante`,
  `MikroTik`) are how triage prioritizes: feed masscan/zgrab2 output
  through Phase 1 rather than port-number assumptions.

Internet-wide discovery (`masscan 0.0.0.0/0 -p1080 --banners` style
sweeping) is an external-attack-surface exercise — only under
authorization that explicitly covers internet-wide scanning, which most
engagements do not. Against a scoped target, the phases above are the
whole job.

## Findings you can write from this surface

- Open SOCKS relay (no auth + outward reach): abuse as anonymization
  relay; the host's IP appears in third-party abuse.
- Open SOCKS relay with *inward* reach: full pivot into the segment the
  proxy host sits in — demonstrate with one internal request and stop.
- Weak credentials via socks-brute/hydra: credential finding plus the
  pivot above.
- SOCKS daemon of unexplained provenance on a server: incident
  indicator, not a config finding — escalate per engagement rules.

## Tool appendix (prose)

nmap NSE socks-open-proxy and socks-brute; hydra's socks5 module; curl
(`--socks5-hostname`), proxychains4, and nmap `-sT` for relayed
enumeration; masscan/zgrab2 for internet-wide banner work where
authorized.
