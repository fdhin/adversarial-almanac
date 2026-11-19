---
name: "httprint"
categories:
  - blackarch-fingerprint
version: "301"
homepage: "https://www.net-square.com/httprint.html"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2025-12-16"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/httprint"
github-stars: 3
github-pushed: "2019-03-24"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# httprint

> Classic web-server fingerprinter from net-square that identifies server software via statistical signature matching.

## What it's for

httprint (net-square, v0.301 beta) fingerprints web servers by sending probes
and statistically matching the responses against a signature file
(`signatures.txt`, with the printed report giving a confidence percentage). It
works even when the `Server` header has been changed or suppressed, accepts
single hosts, IP ranges, plain text lists and Nmap XML output as input, and
reports in HTML, CSV or XML. It can auto-detect SSL per port and tune
timeouts, retries, thread count and User-Agent. Free for non-commercial,
personal or educational use. It is a fingerprinter only — it does not scan
ports itself (feed it an Nmap XML file for that) and does not version
individual web applications. The net-square release has no source repository;
it is mirrored on GitHub at Seabreg/httprint, whose packaged files (the
`httprint` binary and `signatures.txt`) are byte-identical to the upstream
archive.

## Typical use cases

### Fingerprint a single web server

```
httprint -h www.example.com -s signatures.txt
```

Outcome: prints the identified server software and version with a confidence
percentage per candidate. Use a full URL form (`-h https://www.example.com/`,
`-h http://www.example.com:8080/`) to force the scheme or port; SSL detection
is automatic unless `-noautossl` is given.

### Sweep an address range to HTML report

```
httprint -h 198.51.100.1-198.51.100.254 -s signatures.txt -o report.html -th 16
```

Outcome: fingerprints every host in the range (default ports) with 16 worker
threads and writes an HTML report with per-host results, including the server
icons where recognized.

### Fingerprint every HTTP port found by Nmap

```
httprint -x scan.xml -s signatures.txt -oc report.csv
```

Outcome: reads the Nmap `-oX` output, selects the ports listed in
`nmapportlist.txt` as HTTP candidates, fingerprints each open HTTP service and
writes a CSV report; use `-ox report.xml` for XML output instead.

*No corpus evidence — derived from upstream documentation.*
