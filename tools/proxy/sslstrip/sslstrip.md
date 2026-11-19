---
name: "sslstrip"
categories:
  - blackarch-proxy
version: "0.9"
homepage: "https://github.com/moxie0/sslstrip"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "moxie0/sslstrip"
github-stars: 2082
github-pushed: "2021-05-29"
github-archived: false
github-issues: 27
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sslstrip

> Classic MITM tool that downgrades victim HTTPS links to HTTP and logs the plaintext.

## What it's for

sslstrip implements Moxie Marlinspike's SSL-stripping attacks: placed as an HTTP proxy in front of a victim (typically via ARP spoofing plus an iptables REDIRECT of port 80), it rewrites HTTPS references in proxied pages to HTTP, accepts the victim's plaintext traffic, and speaks TLS to the real server on its behalf — logging URLs, cookies and submitted credentials as they pass through. It is Python 2/Twisted software implementing the 2009-era attack; modern sites protected by HSTS and preload lists largely defeat it, so its main value today is understanding and demonstrating the technique. It does not attack TLS itself — it relies on the victim never noticing the downgrade.

## Typical use cases

### Intercept a victim's traffic and strip HTTPS

Full ARP-spoof interception chain.

```
echo "1" > /proc/sys/net/ipv4/ip_forward
iptables -t nat -A PREROUTING -p tcp --destination-port 80 -j REDIRECT --to-port 10000
sslstrip -l 10000 -w sslstrip.log
arpspoof -i eth0 -t 198.51.100.10 198.51.100.1
```

Outcome: with the host forwarding packets and answering ARP for the gateway, the victim's port-80 traffic lands on sslstrip's listener; HTTPS links in returned pages are rewritten to HTTP, and the observed URLs and POSTed credentials accumulate in sslstrip.log.

### Record everything with the cosmetic fixes

Log all traffic — not just SSL POSTs — with session-killing and favicon spoofing.

```
sslstrip -l 10000 -a -f -k -w full.log
```

Outcome: the proxy logs all HTTP and stripped-SSL traffic (`-a` raises logging from the POST-only default), substitutes a lock favicon on secure-looking requests so a spoofed padlock still appears in the victim's browser (`-f`), and kills sessions in progress (`-k`) to force re-authentication through the proxy; everything is written to full.log.

*No corpus evidence — derived from upstream documentation.*
