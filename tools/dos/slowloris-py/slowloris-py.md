---
name: "slowloris-py"
categories:
  - blackarch-dos
version: "30.eb7f632"
homepage: "https://github.com/gkbrk/slowloris"
packaged-by: [blackarch]
blackarch-builddate: "2019-05-05"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "gkbrk/slowloris"
github-stars: 2846
github-pushed: "2024-06-01"
github-archived: false
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# slowloris-py

> Pip-installable Python 3 slowloris: holds web server sockets open with dribbled partial headers.

## What it's for

This is Gökberk Yaltıraklı's Python 3 implementation of the slowloris technique — an independent port of the classic Perl [[slowloris]], not the same codebase. It opens many HTTP connections to a threaded web server, sends partial request headers, then tops each connection up every ~15 seconds so the requests never complete but never time out either; once the server's connection pool is full it cannot serve anyone else, all at trivial bandwidth. It installs from PyPI as `slowloris` (BSD-style flags: port, socket count, HTTPS, SOCKS5 proxy, randomized user agents). Use it only against servers you own or are authorized to test.

## Typical use cases

### Basic run against a host

Open the default number of slow connections with no options.

```
slowloris example.com
```

Outcome: sockets are opened to example.com:80, each sending partial headers and re-sending keep-alive headers periodically; run it until the server stops accepting new clients, then Ctrl-C to release.

### Set port, socket count, and header pace

Control the exhaustion rate against a specific listener.

```
slowloris example.com -p 8080 -s 500 --sleeptime 10
```

Outcome: 500 sockets (`-s`) target port 8080 (`-p`) with a header top-up every 10 seconds (`--sleeptime`) — shorter sleep keeps lazier servers from timing the connections out.

### Attack an HTTPS listener

Run the same hold-open pattern over TLS.

```
slowloris example.com --https -p 443
```

Outcome: connections are made over TLS to port 443 and held open with the same partial-header dribble — relevant because TLS handshakes make each held connection cost the server more.

### Route through a SOCKS5 proxy

Source the connections from a proxy instead of your own address.

```
slowloris example.com -x --proxy-host 127.0.0.1 --proxy-port 9050
```

Outcome: every socket connects through the SOCKS5 proxy at 127.0.0.1:9050 (`-x` enables proxying; host/port flags override the 127.0.0.1:8080 defaults) — requires the PySocks package.

### Look like many browsers while holding sockets

Randomize the user agent on every request for stealthier testing.

```
slowloris example.com -ua -v
```

Outcome: each request carries a randomized user agent (`-ua`) and verbose logging (`-v`) prints per-connection activity to the terminal so you can watch sockets being opened, topped up, and re-opened.

*No corpus evidence — derived from upstream documentation.*
