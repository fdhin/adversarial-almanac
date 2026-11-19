---
name: "tunna"
categories:
  - blackarch-networking
version: "41.cba006d"
homepage: "https://github.com/SECFORCE/Tunna"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "SECFORCE/Tunna"
github-stars: 1279
github-pushed: "2022-09-04"
github-archived: false
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tunna

> SECFORCE's HTTP tunnel — wraps arbitrary TCP connections inside HTTP requests to a webshell on the target, as a local SOCKS proxy or a fixed port-forward, for networks where only the webserver port egresses.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Tunneling TCP out of environments where inbound and outbound connections are blocked except the webserver port (typically 80/443). Two halves cooperate: a webshell (PHP/ASPX/JSP/JSPX variants ship with the tool) uploaded to the target, which reads data from a local service and returns it in HTTP responses; and a local proxy (`proxy.py`, packaged as `tunna-proxy`) that unwraps those responses and re-wraps client data into HTTP POSTs. The prerequisite is the ability to upload the webshell — without it there is nothing to tunnel through. Being proof-of-concept code, it can destabilize the webserver, and JSP shells typically need a reduced buffer size to avoid dropped bytes.

## Typical use cases

### SOCKS proxy over a webshell

```
tunna-proxy -u http://target.example.com/conn.aspx -l 8000 -v
```

Outcome: starts a local SOCKS proxy on port 8000 whose connections are carried as HTTP to the webshell at the given URL and relayed from the target — any SOCKS-aware tool can now reach hosts as seen from the target. `-v` prints packet sizes as traffic flows.

### Fixed forward to a remote service (no SOCKS)

```
tunna-proxy -u http://target.example.com/conn.aspx -l 4444 -r 3389 -b 8192 --no-socks -v
```

Outcome: local port 4444 becomes a direct pipe to port 3389 on the target's loopback (webshell connects locally); point an RDP client at `localhost:4444`. `-b` caps the HTTP request size to respect webshell limits.

### Tunnel through an authenticating upstream proxy

```
tunna-proxy -u http://target.example.com/conn.aspx -l 8000 -x http://198.51.100.10:3128 -A -v
```

Outcome: the HTTP carrier itself goes through the corporate proxy at 198.51.100.10:3128, with `-A` prompting for its credentials — the whole tunnel still looks like plain web traffic to that middlebox.

### Forward a banner-first service such as SSH

```
tunna-proxy -u http://target.example.com/conn.aspx -l 2222 -r 22 --no-socks -s -v
```

Outcome: `-s` (`--start-ping`) starts the webshell keep-alive ping thread before the first client connects, so server-first protocols like SSH get their banner through the HTTP polling loop; then `ssh -p 2222 user@127.0.0.1` completes over the tunnel.
