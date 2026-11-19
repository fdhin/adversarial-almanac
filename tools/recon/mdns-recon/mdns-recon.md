---
name: "mdns-recon"
categories:
  - blackarch-recon
version: "11.69b864e"
homepage: "https://github.com/chadillac/mdns_recon"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "chadillac/mdns_recon"
github-stars: 87
github-pushed: "2022-02-15"
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

# mdns-recon

> Unicast mDNS prober that reveals which hosts leak multicast DNS replies to strangers.

## What it's for

A small Scapy script (Python 2 era, last touched 2022) built for the research around CERT VU#550620: mDNS daemons misconfigured to answer unicast queries from outside their link. It sends unicast mDNS service-discovery queries to a target IP and dumps the raw replies, which can expose hostnames, MAC addresses, network details, and device inventory (NAS model/serial, printers, software versions). It is a raw research probe, not a pretty reporter — output is unfiltered reply bytes plus a per-record length summary. For readable, single-target checks `dig +short @198.51.100.10 -p 5353 -t any _services._dns-sd._udp.local` gives the same service list; this tool's value is scripting it across many hosts. No corpus evidence — derived from upstream documentation.

## Typical use cases

### Probe a single host

Check whether one device answers unicast mDNS.

```
mdns-recon 198.51.100.10
```

Outcome: prints a `198.51.100.10 - START` ... `- END` block containing each mDNS record it received (e.g. `_services._dns-sd._udp.local`, `_ipp._tcp.local`, `_http._tcp.local`) with raw reply contents, ending with a dict mapping each service record to its reply length. An empty block means the host ignored the unicast query — the desired, patched behavior.

### Sweep a subnet and log replies

Find leaking mDNS responders across a range.

```
for octet in {1..254}; do echo "198.51.100.$octet"; done | xargs -I{} mdns-recon {} >> mdns-replies.txt
```

Outcome: runs the probe against every address in the /24 in sequence, appending each host's START/END block to `mdns-replies.txt`; afterwards, `grep -c ' - START' mdns-replies.txt` counts responders and grepping for record names shows which leaked device types (printers, NAS, workstations) replied.
