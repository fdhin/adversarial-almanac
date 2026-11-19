---
name: "udp-hunter"
categories:
  - blackarch-scanner
version: "4.b95cce5"
homepage: "https://github.com/NotSoSecure/udp-hunter"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "NotSoSecure/udp-hunter"
github-stars: 117
github-pushed: "2020-02-26"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# udp-hunter

> UDP service assessment probe suite covering IPv4 and IPv6.

## What it's for

UDP Hunter (NotSoSecure) is a Python 3 network assessment tool focused on UDP services that are painful to scan reliably: it sends protocol-aware probes for 19 services on their default ports (IKE, RPC, NTP, SNMP, MS-SQL, TFTP, DB2, Citrix, echo, chargen, systat, daytime/time, DNS, NBTStat, XDMCP, net-support, mDNS, GTPv1), then sniffs the interface for replies and reports what responded — each finding comes with exploitation guidance from a bundled help database. It accepts single hosts or IP files (domains are resolved), per-probe and per-port filtering, and explicit IPv4/IPv6 listener addresses for dual-stack work. Run it from a position where return UDP traffic actually reaches your interfaces.

## Typical use cases

### Probe every supported service on one host

```bash
udp-hunter --host 198.51.100.10 --output report.txt
```

Outcome: all 19 probes fire at the host and a text report lists every service that answered, each with tool suggestions and next-step exploitation hints drawn from `udphelp.txt`.

### Sweep a target list with selected probes only

```bash
udp-hunter --file targets.txt --output report.txt --probes=NTPRequest,SNMPv3GetRequest --ports=123,161
```

Outcome: only the chosen probes hit the chosen ports across every listed IP — a targeted check for known exposures instead of the full suite.

### Hunt over IPv6 with an explicit listener

```bash
udp-hunter --host 2001:db8::10 --lhost6 2001:db8::1 --noise=true --timeout 2.0
```

Outcome: probes the IPv6 target with capture bound to the given listener interface address; `--noise=false` restricts reported traffic to hosts and ports in the target list, and `--timeout` caps the capture window in minutes.

*No corpus evidence — derived from upstream documentation (NotSoSecure/udp-hunter README features, options and usage lines; BlackArch PKGBUILD wraps `udp-hunter.py` as `udp-hunter`).*
