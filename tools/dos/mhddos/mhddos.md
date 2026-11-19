---
name: "mhddos"
categories:
  - dos
version: "git (2026-11-03)"
homepage: "https://github.com/matrixtm/mhddos"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "matrixtm/mhddos"
github-stars: 16992
github-pushed: "2026-11-03"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 69
gh-activity-checked: "2026-11-09"
worth: 7.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# mhddos

> Python stress-testing toolkit bundling dozens of Layer 3/4/7 flood methods plus a utility console.

## What it's for

MHDDoS (`start.py`, Python 3.11+) is a multi-vector load and denial-of-service testing toolkit for use against infrastructure you own or are authorized to test. It exposes roughly 60 methods in three groups: Layer 7 HTTP floods (GET/POST, header tricks, bypasses aimed at specific anti-DDoS providers, a slowloris-style SLOW mode), Layer 4/3 floods (TCP, UDP, SYN, ICMP, several game/status protocols, and amplification vectors such as DNS, NTP, MEM, CLDAP, RDP), and utility commands. Attacks can run directly or through SOCKS4/SOCKS5/HTTP proxy lists, which the tool can download and health-check itself; a bundled web panel and a tools console (DNS lookup, ping, site status, proxy checker, Cloudflare real-IP finder) support the workflow. It is not a measuring instrument — it reports traffic sent, not server-side latency or capacity.

## Typical use cases

### List methods and built-in examples

See every available method and the argument layout before launching anything.

```
python3 start.py HELP
```

Outcome: prints the usage block with all Layer 4, Layer 7, tools and commands, plus ready-made example command lines for each attack family.

### Layer 7 HTTP GET flood

Saturate a web application with concurrent HTTP GET requests, optionally through proxies.

```
python3 start.py GET http://example.com 0 100 http.txt 10 60
```

Outcome: floods http://example.com for 60 seconds from 100 threads at 10 requests-per-connection. Positional arguments are socks type (`0` = all, `4`/`5` = SOCKS4/5, `1` = HTTP, `6` = random), threads, proxy-list filename (resolved under `files/proxies/`), RPC, and duration in seconds. If the proxy file is empty the attack runs direct; if it does not exist, proxies are downloaded and checked first.

### Layer 4 UDP flood against a host:port

Fill a service's pipe with raw UDP traffic for a fixed window.

```
python3 start.py UDP 198.51.100.10:80 100 60
```

Outcome: 100 threads send UDP packets to 198.51.100.10 port 80 for 60 seconds; Layer 4 syntax is method, `ip:port`, threads, duration.

### Route a TCP flood through SOCKS5 proxies

Hide the load source behind a proxy list.

```
python3 start.py TCP 198.51.100.10:80 100 60 5 socks5.txt
```

Outcome: the TCP flood runs through SOCKS5 proxies taken from `socks5.txt` (same `files/proxies/` directory and auto-download behavior as Layer 7).

### DNS amplification test

Exercise amplification-method behavior against an authorized reflector setup.

```
python3 start.py DNS 198.51.100.10:53 100 60 dns.txt
```

Outcome: runs the DNS amplification method against the target for 60 seconds using the specified list file; swap the method name for NTP, MEM, CLDAP, or RDP to test other vectors.

### Open the tools console

Use the bundled reconnaissance/check utilities without running any flood.

```
python3 start.py tools
```

Outcome: an interactive console with CFIP (find a Cloudflare-fronted site's real IP), DNS, TSSRV, PING, CHECK, DSTAT, PROXYCHECK, and HEADERS; `python3 start.py STOP` halts any methods still running.

*No corpus evidence — derived from upstream documentation.*
