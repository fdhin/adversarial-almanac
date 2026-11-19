---
name: "sslnuke"
categories:
  - blackarch-cracker
version: "5.c5faeaa"
homepage: "https://github.com/jtripper/sslnuke"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-06"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "jtripper/sslnuke"
github-stars: 77
github-pushed: "2013-09-10"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sslnuke

> Transparent interception proxy that decrypts "SSL-secured" IRC traffic routed through it and prints the plaintext to stdout.

## What it's for

sslnuke (jtripper) demonstrates how weak SSL verification is outside the browser: many IRC clients skip certificate verification, so a host in the traffic path can terminate the SSL connection and read everything. sslnuke transparently proxies IRC-over-SSL — it auto-detects whether a client is using SSL, connects onward to the real server, and logs the decrypted messages as they flow. It is deployed on a gateway or host whose outbound traffic is redirected into it with an iptables REDIRECT rule, optionally combined with ARP poisoning on a LAN. It is not an HTTP downgrade ("SSL stripping") tool — it decrypts SSL rather than removing it — and its README documents the certificate-pinning mitigation that defeats it.

## Typical use cases

### Intercept outbound IRC-over-SSL

Redirect the interesting traffic into sslnuke and run it.

```
iptables -t nat -A OUTPUT -p tcp -m owner ! --uid-owner 1000 -m tcp --dport 6697 --tcp-flags FIN,SYN,RST,ACK SYN -j REDIRECT --to-ports 4444
sslnuke
```

Run sslnuke itself as the user excluded by the rule (uid 1000 here) so its own upstream connections are not looped back into it.

Outcome: any SSL IRC connection through the host is relayed through the proxy, and the decrypted traffic prints live — connection notices plus each PRIVMSG/NickServ exchange, including nickname passwords.

### Check what an IRC client would see of the server certificate

The README's mitigation step doubles as a test of whether interception would be caught.

```
openssl s_client -showcerts -connect irc.example.com:6697 </dev/null
```

Outcome: dumps the server's real certificate chain, which can be pinned in the client (e.g. irssi's `-ssl_cafile`); a client with the genuine certificate pinned rejects the certificate sslnuke presents, and the interception fails visibly.

*No corpus evidence — derived from upstream documentation.*
