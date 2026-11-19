---
name: "a2sv"
categories:
  - blackarch-scanner
version: "140.cb24c4e"
homepage: "https://github.com/hahwul/a2sv"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-02"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "hahwul/a2sv"
github-stars: 636
github-pushed: "2020-12-12"
github-archived: true
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# a2sv

> Auto Scanning to SSL Vulnerability — one-command checks for the 2014–2016 OpenSSL attack wave (Heartbleed, CCS injection, POODLE, FREAK, LOGJAM, DROWN, CRIME).

*No corpus evidence — derived from upstream documentation.*

## What it's for

a2sv drives the OpenSSL client against a TLS endpoint and probes it for the classic named vulnerabilities: Heartbleed (CVE-2014-0160), CCS injection (CVE-2014-0224), SSLv3 POODLE (CVE-2014-3566), FREAK (CVE-2015-0204), LOGJAM (CVE-2015-4000), SSLv2 DROWN (CVE-2016-0800), CRIME over SPDY (CVE-2012-4929) and anonymous-cipher suites (CVE-2007-1858). One command runs the whole battery or a single module, against one host or a list read from a file, and prints a per-check verdict. It is a lightweight Python script whose upstream was archived in 2020 — it knows nothing of TLS 1.3 or post-2016 weaknesses, so read a clean result as "none of the legacy CVEs present", not "this TLS stack is healthy"; a maintained scanner is the right tool for a current assessment.

## Typical use cases

### Run the full SSL vulnerability battery

```
a2sv -t 198.51.100.10
```

Outcome: Probes port 443 with every module in turn, printing each check's name and its verdict line as it completes, and finishes with a summary of which of the eight vulnerabilities the endpoint exhibits.

### Scan a non-standard TLS port

```
a2sv -t 198.51.100.10 -p 8443
```

Outcome: The same checks run against the specified port instead of the default 443 — for HTTPS services on management, development or otherwise alternate ports.

### Run a single check

```
a2sv -t 198.51.100.10 -m heart
```

Outcome: Only the Heartbleed probe runs and reports. Module keys are `anonymous`, `crime`, `heart`, `ccs`, `poodle`, `freak`, `logjam`, `drown` — one suspected CVE can be re-tested quickly without the full battery (the upstream README's `-m heartbleed` example is stale; the source dispatches on `heart`).

### Scan a list of hosts

```
a2sv -tf targets.txt
```

Outcome: Every address in the file is scanned in sequence with the full module set, producing a per-host verdict block — a quick sweep to find which TLS endpoints across a range still carry the legacy CVEs.

### Save results to a file

```
a2sv -t 198.51.100.10 -o results.txt
```

Outcome: The findings are also written to the named file, preserving the per-module verdicts for reporting instead of scrolling past in the terminal.
