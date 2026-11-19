---
name: "massbleed"
categories:
  - blackarch-recon
version: "20.44b7e85"
homepage: "https://github.com/1N3/MassBleed"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "1N3/MassBleed"
github-stars: 250
github-pushed: "2020-04-18"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# massbleed

> Mass SSL/TLS vulnerability sweep for the 2014-2016 OpenSSL-era CVEs across whole CIDRs.

## What it's for

A bash scanner (by 1N3 of CrowdShield) that combines unicornscan, nmap and sslscan to test many hosts at once for five dated but historically widespread TLS weaknesses: Heartbleed (CVE-2014-0160), OpenSSL CCS injection (CVE-2014-0224), POODLE SSLv3 (CVE-2014-3566), WinShock/SChannel (MS14-066) and DROWN (CVE-2016-0800). You hand it a CIDR or single IP plus a mode keyword and it reports vulnerable services; it can also route everything through proxychains with a trailing `proxy` argument. It is era-specific: for a modern TLS assessment use testssl.sh or sslscan directly — massbleed's niche is confirming that ancient, unpatched OpenSSL stacks still exist across a large range. It is not a general SSL inventory tool and checks only its five signatures.

## Typical use cases

### Sweep a CIDR for vulnerable HTTPS

Test every host in a range on port 443.

```
massbleed 198.51.100.0/24
```

Outcome: the default mode scans the CIDR over 443/tcp and prints findings per host for each of the five CVE checks; only hosts running a vulnerable service appear in the output.

### Scan a custom TLS port

Check services on non-standard ports such as 8443.

```
massbleed 198.51.100.0/24 port 8443
```

Outcome: `port` mode runs the same checks against the given TCP port instead of 443 — useful for admin panels, Tomcat or database TLS listeners.

### Audit every port on one host

Find TLS services hiding on high ports of a single system.

```
massbleed 198.51.100.10 single
```

Outcome: `single` mode enumerates ports 1-10000 on the one host and tests each TLS service it finds for the supported vulnerabilities.

### Sweep a class C subnet on all ports

Cover every host of a /24, every open port.

```
massbleed 198.51.100. subnet
```

Outcome: `subnet` mode takes the partial class C prefix (note the trailing dot) and scans all open ports on every host in that /24 — the slowest but most complete mode. Append a final `proxy` argument to any of these forms to send the traffic through proxychains.
