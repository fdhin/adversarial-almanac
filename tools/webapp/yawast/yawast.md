---
name: "yawast"
categories:
  - blackarch-webapp
version: "0.12.1"
homepage: "https://pypi.org/project/yawast-ng/#files"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: python
license: BSD
meta-checked: "2026-11-09"
github-repo: "Numorian/yawast-ng"
github-stars: 2
github-pushed: "2025-10-27"
github-archived: false
github-issues: 18
github-match: "search"
gh-checked: "2026-11-09"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# yawast

> The YAWAST Antecedent Web Application Security Toolkit — a first-look recon scan (TLS, headers, files, directories, DNS) in one command.

## What it's for

yawast-ng ("YAWAST ...where a pentest starts", by Adam Caudill; the Next Generation continuation of the original YAWAST project by the same author) simplifies the initial analysis and information gathering phase of a web assessment. One command runs checks across TLS/SSL (versions, cipher suites, common flaws, with SSL Labs integration), information disclosure, missing security headers, presence of files and directories that indicate issues, common vulnerabilities, and DNS records (subdomains, SRV). It is explicitly not a full testing suite: it collects the data that lets you target further tests, pairing well with Burp Suite via `--proxy`. The console command installed by the distro package is `yawast`; findings can be exported as JSON.

## Typical use cases

### Run a full initial scan

```
yawast scan http://target.example.com/
```

Outcome: a categorized report covering TLS/SSL issues, information disclosure, missing security headers and other baseline checks for the target, printed to the terminal.

### Deepen file and directory discovery

```
yawast scan --dir --dirrecursive --files http://target.example.com/
```

Outcome: the scan additionally enumerates common directories (recursively) and probes a large list of common files, reporting what is unexpectedly present.

### Audit only TLS/SSL

```
yawast ssl http://target.example.com/
```

Outcome: focused TLS/SSL output — supported versions and cipher suites plus common issues; `--internalssl` disables the SSL Labs integration and `--tdessessioncount` adds a SWEET32 session-message count. The `dns` subcommand does the same split for DNS records (`--subdomains`, `--srv`).

### Route the assessment through Burp

```
yawast scan --proxy http://127.0.0.1:8080 --output results.json http://target.example.com/
```

Outcome: every request goes through the proxy so each check is inspectable in Burp, and the complete findings set is also written to `results.json` for later processing.

*No corpus evidence — derived from upstream documentation (README and `yawast/command_line.py` of Numorian/yawast-ng, the PyPI source the package builds).*
