---
name: "uppwn"
categories:
  - blackarch-webapp
version: "9.f69dec4"
homepage: "https://github.com/ferrery1/UpPwn"
packaged-by: [blackarch]
blackarch-builddate: "2019-08-11"
build-system: node
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "ferrery1/UpPwn"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# uppwn

> Python 2 Selenium script that automates detection — and sometimes exploitation — of security flaws in websites' file-upload systems.

## What it's for

UpPwn (ferrery1) automates testing of web file-upload functionality: it drives a real browser through a site's upload forms to detect security flaws in how uploads are validated and, in some cases, exploits them by uploading malicious files. It targets the upload subsystem specifically rather than being a general web scanner. It is a version 0.1 dev-stage Python 2 tool: its only documented command-line surface is the help flag, with everything else driven interactively via Selenium against Firefox, and the upstream repository has since disappeared (github-status: gone), so the packaged snapshot is the remaining reference. Expect its detection logic to reflect the upload-filtering landscape of its era.

## Typical use cases

### Launch an interactive upload-flaw scan

Start the tool; the rest is interactive.

```
uppwn
```

Outcome: starts the Selenium-driven flow (Firefox must be installed); you point it at a target upload page and it walks its upload-security checks, reporting flaws it detects and, where supported, attempting the malicious-upload proof. `uppwn -h` prints the only documented command-line help.

No corpus evidence — derived from upstream documentation (archived README of the former ferrery1/UpPwn repository, via Wayback Machine) and the packaged launcher.
