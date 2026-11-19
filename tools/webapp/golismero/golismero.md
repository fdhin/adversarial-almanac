---
name: "golismero"
categories:
  - blackarch-webapp
version: "73.7d605b9"
homepage: "https://github.com/golismero/golismero"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "golismero/golismero"
github-stars: 883
github-pushed: "2020-11-03"
github-archived: true
github-issues: 28
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# golismero

> Plugin-driven web security testing framework that orchestrates its own audits plus external scanners, with unified reporting.

## What it's for

GoLismero is a modular security-testing framework geared toward web pentesting: it runs audit plugins (spidering, DNS analysis, brute force, web attack patterns) and also imports and unifies results from well-known external tools such as sqlmap, xsser, OpenVAS, dnsrecon and theharvester, then renders findings into self-contained text/RST/HTML reports mapped to CWE/CVE/OWASP references. It is pure Python 2 and its upstream has been archived since 2020, so treat it as historical/lab tooling rather than a modern go-to. Scan, import and report phases share a SQLite database, so a scan can be reported on later or from another machine.

## Typical use cases

### Run a full default web audit with an HTML report

```
golismero scan http://target.example.com -o report.html
```

Outcome: launches the default plugin set against the target, prints findings to the console and writes a single self-contained `report.html` ready to hand over.

### Import third-tool results and scan with a plugin subset

```
golismero -i nmap_output.xml -e dns* -db database.db -o report.rst
```

Outcome: imports the Nmap output into `database.db`, runs only the DNS-analysis plugins, and emits a reStructuredText report — the `scan` command is inferred automatically from the options given.

### List plugins and inspect one before using it

```
golismero plugins
```

Outcome: prints every available plugin with its category and description; `golismero info <plugin>` then shows one plugin's details before you target it selectively with `-e <plugin>`.

No corpus evidence — derived from upstream documentation.
