---
name: "httprint-win32"
categories:
  - blackarch-windows
version: "301"
homepage: "http://net-square.com/httprint"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: windows
license: custom
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# httprint-win32

> Signature-based web-server fingerprinter that identifies servers despite a scrubbed or forged banner.

## What it's for

httprint (net-square, build 301) fingerprints web servers from their behavioral characteristics — how they handle malformed requests, header ordering and status codes — and matches them against a text signature database with fuzzy-logic confidence ratings, so it still identifies servers whose banner was patched or hidden by modules such as mod_security or ServerMask. It also inventories banner-less web devices (printers, routers, access points), auto-detects SSL and gathers certificate details, follows 301/302 redirects, can import nmap XML output, and writes HTML, CSV or XML reports. The package ships the Win32 command-line binary `httprint.exe` plus a GUI (`httprint_gui.exe`) and the editable `signatures.txt`; it is free for non-commercial use, and the upstream site is long gone, so the bundled readme is the reference.

## Typical use cases

### Fingerprint a single host

Identify what a web server really runs.

```
httprint -h target.example.com -s signatures.txt
```

Outcome: probes the target, compares responses against the signature file, and writes `httprintoutput.html` (default name) ranking the best-matching server products with confidence percentages — banner and actual identification often differ, which is the point.

### Fingerprint an HTTPS service or odd port

Handle TLS endpoints and non-standard ports via URL syntax.

```
httprint -h https://target.example.com:8443/ -s signatures.txt
```

Outcome: SSL is auto-detected (certificate details are collected and included in the report); add `-noautossl` when auto-detection misfires on a plain-HTTP port.

### Sweep a range or a host list

Fingerprint many hosts in one run.

```
httprint -h 198.51.100.1-198.51.100.254 -s signatures.txt -o report.html -th 16
```

Outcome: scans the IP range in parallel (default 8 threads, maximum 64; `-th 16` raises it) and writes the combined report to `report.html`; alternatively feed a text file of hosts/URLs with `-i input.txt`.

### Import an nmap scan and export CSV

Turn port-scan results into a server inventory.

```
httprint -x scan.xml -s signatures.txt -oc report.csv
```

Outcome: reads nmap's `-oX` XML output, fingerprints every host whose open ports look like HTTP (per the bundled `nmapportlist.txt`), and writes a CSV inventory of identified servers — `-ox report.xml` produces XML instead.

*No corpus evidence — derived from upstream documentation.*
