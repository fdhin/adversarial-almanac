---
name: "3proxy"
categories:
  - blackarch-proxy
version: "0.9.6"
homepage: "https://github.com/3proxy/3proxy"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-06-29"
build-system: make
license: Apache-2.0, GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "3proxy/3proxy"
github-stars: 5578
github-pushed: "2026-11-07"
github-archived: false
github-issues: 29
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 458
gh-activity-checked: "2026-11-09"
---

# 3proxy

> A tiny multi-protocol proxy server — one binary that speaks HTTP/HTTPS CONNECT, SOCKS4/5, POP3/FTP/SMTP proxying, TCP/UDP port mapping and SNI proxying, all driven by a plain-text config.

## What it's for

Turning any host into a proxy or relay. It is configured by a list of service commands in a text file rather than flags, and one process runs many listeners at once: an HTTP proxy on one port, SOCKS5 on another, plus port mappings. Typical roles: a lightweight pivot on a foothold host, a quick rate-limited proxy for a team, or infrastructure building blocks (DNS caching, SNI routing, chaining through parent proxies). Dedicated single-purpose frontends ship alongside it — `tcppm`/`udppm` for TCP/UDP port mapping and `tlspr` as an SNI proxy. It is not an encrypted tunnel by itself; wrap it if the transport is hostile.

## Typical use cases

### Run HTTP and SOCKS5 listeners from one config

Create `3proxy.cfg`:

```
nserver 8.8.8.8
nscache 65536
log
proxy -p3128
socks -p1080
```

Then start it:

```
3proxy 3proxy.cfg
```

Outcome: one process with an HTTP proxy on port 3128 and a SOCKS5 proxy on port 1080, both accepting connections. `nserver`/`nscache` give it independent DNS resolution with caching; `log` sends connection logs to stdout. Point a browser, `curl --proxy`, or proxychains at either port.

### Register it as a Windows service

```
3proxy 3proxy.cfg --install
```

Outcome: 3proxy is registered as a Windows service using that config and starts at boot. Stop it with `net stop 3proxy`, restart with `net start 3proxy`, and unregister with `3proxy --remove` after stopping it.

### Add a user with a daily traffic cap and bandwidth limit

```
add3proxyuser alice s3cret 1024 1048576
```

Outcome: `alice` can authenticate to the HTTP and SOCKS proxies with that password, limited to 1024 MB of traffic per day and 1 Mbps of bandwidth (1048576 bits per second). Users and limits can also be edited directly in the files under `/etc/3proxy/conf/`.
