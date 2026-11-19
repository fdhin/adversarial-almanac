---
name: "naabu"
categories:
  - blackarch-scanner
version: "v2.3.4.r415.g16b3690"
homepage: "https://github.com/projectdiscovery/naabu"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "projectdiscovery/naabu"
github-stars: 6355
github-pushed: "2026-11-07"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 246
gh-activity-checked: "2026-11-09"
---

# naabu

> ProjectDiscovery's fast port scanner: SYN-based scanning with nmap-style port syntax, host discovery probes and JSON output for pipeline use.

## What it's for

naabu is a Go port scanner built for speed and a clean invocation surface: SYN half-open scanning when run as root (connect-scan fallback otherwise), nmap-format port lists including UDP ports, host discovery probes (ARP, ICMP, TCP SYN/ACK ping), JSON-lines output and a config file for default flags. It is the port-discovery stage of the ProjectDiscovery toolchain — its output is designed to feed httpx and friends — and it can hand discovered ports to nmap for service detection instead of reimplementing it. It does not fingerprint services itself beyond that `-sV` bridge, and UDP detection relies on response heuristics (an optional custom payload can be supplied per scan).

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Scan a host's top ports

Quick default sweep of a single host.

```
naabu -host target.example.com
```

Outcome: SYN scan (root privileges) of nmap's top 100 ports, printing each open port as a `host:port` line with a final per-host summary.

### Scan specific TCP and UDP ports

Target an exact port set in nmap syntax.

```
naabu -p 80,443,21-23,u:53 -host target.example.com
```

Outcome: scans the listed TCP ports and ranges plus UDP 53 (UDP ports written `u:N`), reporting everything that answers; `-cp "payload"` sends a custom payload for UDP services that need one to respond.

### Full port range with exclusions

All 65535 ports minus a noisy pair.

```
naabu -p - -exclude-ports 80,443 -host 198.51.100.10
```

Outcome: full-range scan of the host with ports 80 and 443 skipped — `-top-ports 1000` is the middle ground between this and the default top 100.

### Scan a host list with machine-readable output

Feed many targets and consume the results downstream.

```
naabu -list hosts.txt -json
```

Outcome: scans every host in the file and emits one JSON line per open port (`{"ip":"...","port":...}`), ready to pipe into other tools; hosts can also be streamed on stdin, and ASN input works (`echo AS14421 | naabu -p 80,443`).

### Add service versions via nmap

Upgrade discovered ports to service/version data.

```
naabu -host target.example.com -p 80,443,8080 -sV
```

Outcome: after finding open ports, naabu invokes nmap against exactly those ports and attaches nmap's service and version detection to the results.

### Host discovery only

Ping-sweep a subnet without port scanning.

```
naabu -host 198.51.100.0/24 -sn -pe
```

Outcome: ICMP echo discovery across the subnet listing live hosts only; `-arp`, `-nd`, `-ps 80`, `-pa 443`, `-pp`, `-pm` select the other probe types.
