---
name: "dcdetector"
categories:
  - blackarch-networking
version: "0.0.1.r52.g2e69244"
homepage: "https://github.com/noraj/DCDetector"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: ruby
license: MIT
meta-checked: "2026-11-09"
github-repo: "noraj/DCDetector"
github-stars: 19
github-pushed: "2024-06-23"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dcdetector

> Find every domain controller in an Active Directory domain by querying its DNS SRV records.

*No corpus evidence — derived from upstream documentation.*

## What it's for

DC Detector (a Ruby gem by Alexandre ZANNI, noraj) enumerates all domain controllers of an Active Directory domain by asking the domain's DNS server for the SRV records that advertise DCs, then resolving them. It prints three lists — DC computer names, FQDNs, and IP addresses — which is typically the first foothold question in any AD engagement: where do I point Kerberos/LDAP tools? Point it at the internal DNS server with `-s` when your system resolver cannot resolve the target domain's records. Note: the upstream is archived and marked deprecated; its functionality lives on in the author's AD Assault CLI (`dns find_dcs`) and library class — prefer that for new work.

## Typical use cases

### Enumerate DCs via a known DNS server

Resolve the domain's controllers through the domain DNS.

```
dcd -d corp.example.com -s 198.51.100.10
```

Outcome: queries 198.51.100.10 for the domain's DC SRV records and prints the DC names, their FQDNs, and all resolved IP addresses. Both binary names work: `dcd` and `dcdetector`.

### Long-form flags and color control

Same lookup with explicit options.

```
dcdetector --domain corp.example.com --nameserver 198.51.100.10 --no-color
```

Outcome: identical output with long flags; `--no-color` strips the colorization (useful for piping into other tools) and `--debug` prints the parsed arguments. Without `--nameserver` the system resolver is used.

### Use it as a Ruby library

Embed DC discovery in your own tooling.

```
require 'dcdetector'
dcd = DCDetector::App.new('corp.example.com', nameserver: ['198.51.100.10'])
dcd.dc_fqdn
```

Outcome: `dc_fqdn`, `dc_ip`, and `dc_name` return arrays (for example `["dc01.corp.example.com"]`) that you can feed directly into other AD tooling.
