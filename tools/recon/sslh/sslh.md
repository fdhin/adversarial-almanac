---
name: "sslh"
categories:
  - information-gathering
  - post-exploitation
  - web
version: "2.3.1-1"
homepage: "https://www.rutschle.net/tech/sslh/README.html"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-04-21"
build-system: autotools
license: GPL-2.0-only
meta-checked: "2026-11-09"
github-repo: "yrutschle/sslh"
github-stars: 5120
github-pushed: "2026-10-27"
github-archived: false
github-issues: 51
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 45
gh-activity-checked: "2026-11-09"
---

# sslh

> Protocol demultiplexer that shares one listening port (classically 443) between SSH, TLS, OpenVPN and more by classifying each connection's first packets.

## What it's for

Letting blocked protocols ride an always-open port. sslh listens on 443 and forwards each connection to the right backend after probing the initial data packet: HTTP, TLS/SSL (including SNI and ALPN), SSH, OpenVPN, WireGuard, tinc, XMPP and SOCKS5 are recognized natively, and any protocol testable with a regular expression can be added. HTTPS keeps working on 443 while SSH or VPN clients reach the same host through firewalls that only allow web traffic. It is a mature daemon — privilege and capability dropping, systemd support, chroot, IPv4/IPv6, TCP/UDP, fork/select/libev execution models — and supports transparent proxying so backends see clients' real IPs, plus HAProxy's PROXY protocol.

## Typical use cases

### Multiplex SSH and HTTPS on port 443

```
sslh --foreground --listen=0.0.0.0:443 --ssh=127.0.0.1:22 --tls=127.0.0.1:443
```

Outcome: sslh owns port 443; SSH clients connecting there are forwarded to sshd on 22 and TLS/HTTP clients to the HTTPS backend. Move the real HTTPS listener to another port (e.g. 8443) and point `--tls` at it; drop `--foreground` to daemonize.

### Run the systemd foreground form

```
sslh -f --ssh 127.0.0.1:22 --tls 127.0.0.1:443
```

Outcome: the exact foreground invocation used in the shipped systemd unit (`-f` mirrors `--foreground`, options space-separated) — the shape to copy into a unit file or debug a config-driven setup interactively. Packaged installations read their listener/protocol settings from `/etc/sslh.cfg`; `example.cfg` in the source tree documents every option.

### Share 443 between TLS backends and a VPN

```
sslh --foreground --listen=0.0.0.0:443 --tls=web.internal:443 --openvpn=vpn.internal:1194
```

Outcome: the TLS probe reads SNI/ALPN from the ClientHello before routing, so an OpenVPN service and HTTPS backends coexist behind one external address and port. Enable transparent proxying or PROXY protocol (both documented in the upstream `doc/` guides) when the backends need the client's real IP for logging, ACLs or fail2ban.

*No corpus evidence — derived from upstream documentation (yrutschle/sslh README and doc/config.md).*
