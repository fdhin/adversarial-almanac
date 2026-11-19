---
name: "gtp-scan"
categories:
  - blackarch-scanner
version: "0.7"
homepage: "http://www.c0decafe.de/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# gtp-scan

> Probes networks for hosts speaking GTP (GPRS Tunnelling Protocol) — the tunnel protocol of mobile packet cores — using echo requests.

## What it's for

gtp-scan (Daniel Mende, c0decafe.de, 2011) finds GTP-capable hosts in an address range: it sends
GTP echo requests (versions 1 and 2) to the well-known GTP-C (2123), GTP-U (2152) and GTP-P
(3386) ports over UDP or SCTP and reports every host that answers, with the version and flags it
speaks. Its audience is auditing telecom infrastructure — GGSN/SGSN-style elements of mobile core
networks, where reachable GTP is a live attack surface. It only discovers and versions peers; it
does not attack them. The script is 2011-vintage Python 2 (packaged with a python2 shebang) and
takes an address or CIDR prefix as its single argument.

## Typical use cases

### Sweep a network for GTP speakers

Find every host in a prefix that answers GTP echo requests.

```
gtp-scan 198.51.100.0/24
```

Outcome: probes each host in the prefix on all three GTP ports and prints every responder with
recovery/flags detail; `-w SEC` stretches the cooldown between rounds on slow or lossy networks.

### Probe over SCTP

Use SCTP transport for deployments that expose GTP over it.

```
gtp-scan -s 198.51.100.0/24
```

Outcome: the same echo sweep carried over SCTP instead of UDP; the tool reminds you to raise
`/proc/sys/net/sctp/max_init_retransmits`, otherwise the SCTP scan drags and can skip hosts.

*No corpus evidence — derived from upstream source (gtp-scan.py 0.7 option parser read from the
c0decafe.de tarball; BSD 3-Clause licence corrected from the script header).*
