---
name: "cntlm"
categories:
  - blackarch-proxy
version: "4.b35d55c"
homepage: "https://github.com/bseb/cntlm"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2018-11-14"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "bseb/cntlm"
github-stars: 10
github-pushed: "2015-12-05"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cntlm

> Local authenticating HTTP proxy that performs NTLM against a corporate parent proxy so your tools never have to.

## What it's for

cntlm is an NTLM/NTLM2SR/NTLMv2 authenticating proxy in optimized C. It stands between your applications and one or more corporate proxies that demand Windows Integrated Authentication: clients talk plain HTTP proxy to cntlm on localhost, and cntlm does the NTLM handshake with the parent — caching authenticated connections so the 5-way handshake is paid once, which also makes it far faster and lighter than the old NTLMAPS-style proxies. Essential on Linux and macOS where tools cannot authenticate to the corporate proxy directly. Beyond relaying it offers multiple parent proxies with failover, hash-based instead of plaintext passwords (`-H`), allow/deny ACLs, a stand-alone mode with no parent (a mini Squid), SSH-style `-L` tunnels through the parent, and a limited SOCKS5 interface. Typical deployment: fill in `/etc/cntlm.conf` (Username, Domain, Proxy, Listen), run it as a service, and point browsers, apt and pip at 127.0.0.1:3128.

## Typical use cases

### Generate password hashes for a password-less config

```
cntlm -H
```

Outcome: prompts for the account's password, then prints the PassLM/PassNT/PassNTLMv2 lines to paste into cntlm.conf (use it with explicit `-u`/`-d`) — after that the config carries hashes instead of a plaintext password.

### Front the corporate proxy from the command line

```
cntlm -u user -d CORP -l 3128 proxy.corp.example.com 8080
```

Outcome: cntlm listens on 127.0.0.1:3128 and forwards to proxy.corp.example.com:8080, authenticating as CORP\user (password prompted at startup, or supplied via the config). Point your tools' proxy settings at localhost:3128; the domain may equivalently be given as `-u user@CORP`.

### Tunnel a port through the authenticated parent

```
cntlm -L 127.0.0.1:3389:target.example.com:3389 -c /etc/cntlm.conf
```

Outcome: cntlm opens a local listener on 3389 and relays connections through the parent proxy to target.example.com:3389 — the same syntax as ssh -L, with the source address optional — so tools with no proxy support at all can still reach hosts behind the corporate proxy.

### Serve other machines (gateway mode)

```
cntlm -g -c /etc/cntlm.conf
```

Outcome: instead of binding loopback only, cntlm listens on all interfaces and can act as the shared authenticating proxy for your whole subnet — guard it with `-A` allow rules (`-A 198.51.100.0/24`).

*No corpus evidence — derived from upstream documentation.*
