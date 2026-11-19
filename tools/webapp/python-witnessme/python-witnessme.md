---
name: "python-witnessme"
categories:
  - blackarch-webapp
version: "1.5.0"
homepage: "https://pypi.org/project/witnessme/#files"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "byt3bl33d3r/WitnessMe"
github-match: "exact"
worth: 0.9
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-stars: 758
github-pushed: "2024-09-23"
github-archived: true
github-issues: 23
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# python-witnessme

> Web inventory tool that screenshots pages (and grabs content) across IPs, CIDRs and Nmap output using headless Chromium.

## What it's for

Rapid "what's alive and what does it look like" inventory during large-scope assessments, inspired by EyeWitness. It drives headless Chromium through Pyppeteer: give it IPs, ranges, CIDRs, hostnames, or parsed Nmap XML / Nessus files, and it visits every web endpoint and captures screenshots plus response metadata into a browsable database session. Beyond screenshots, the same headless session can be scripted against targets — fetching pages, extracting links or arbitrary XPath selections — and the package ships companion commands `wmapi` (serves sessions over an API) and `wmdb` (interactive session browser). It is a recon/documentation tool, not a vulnerability scanner.

## Typical use cases

### Screenshot a whole subnet

Inventory every web service in a range.

```
witnessme screenshot 198.51.100.0/24
```

Outcome: probes the default ports (80, 443, 8080, 8443), loads each responding service in headless Chromium and stores screenshots and metadata in a local session database; on completion it prints the session file to open with `wmdb`.

### Screenshot from Nmap output

Skip port scanning you already did.

```
witnessme screenshot scan-results.nmap.xml
```

Outcome: parses the Nmap XML, visits exactly the discovered HTTP/HTTPS services and builds the screenshot session — the standard workflow when the port scan is done with Nmap anyway.

### Tune concurrency and timeouts

Throttle browser tabs on memory-constrained boxes.

```
witnessme screenshot --threads 5 --timeout 30 198.51.100.0/24
```

Outcome: opens at most 5 concurrent browser tabs (default 15; the CLI warns about RAM at high values) with a 30-second per-connection timeout (default 15), trading speed for stability.

### Grab content instead of screenshots

Use the same headless engine for extraction.

```
witnessme grab -l https://target.example.com
```

Outcome: fetches the target through headless Chromium and prints all discovered links (`-l`); use `-x '//title'` to select arbitrary XPath expressions from the rendered page instead — handy for post-render content checks where raw HTTP fetches differ from what a browser sees.

*Provenance: no corpus evidence — derived from upstream documentation (PyPI README + `witnessme/console/witnessme.py` argparse, verified 2026-11).*
