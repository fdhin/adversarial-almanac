---
name: "penbox"
categories:
  - blackarch-automation
version: "81.3b77c69"
homepage: "https://github.com/x3omdax/PenBox"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: MIT
meta-checked: "2026-11-09"
github-repo: "x3omdax/PenBox"
github-stars: 538
github-pushed: "2016-11-04"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# penbox

> Menu-driven "toolbox of toolboxes": a script that categorizes and dispatches ~60 well-known pentest tools (nmap, sqlmap, cupp, reaver, setoolkit, arachni, ...) so you pick attacks from a menu instead of remembering each tool's syntax.

## What it's for

Front-ending other people's tools. PenBox organizes existing tools into categories — Information Gathering (nmap, port scanning, host-to-IP, WordPress user enumeration, CMS scanning, XSStracer, Doork dork auditing), Password Attacks (cupp, ncrack), Wireless (reaver, pixiewps, Bluetooth honeypot), Exploitation (sqlmap, commix, Venom, jboss-autopwn), Sniffing & Spoofing (SSLstrip, pyPISHER), Web Hacking (WordPress/Joomla scanners, file-upload checks, sub-domain scanning, XSS/SQLi scanning, Cloudflare bypass), private-tool style dork lookups, and Post Exploitation (Weeman phishing, POET, shell checkers). It adds no engine of its own: every menu entry shells out to the underlying tool, so its reach is exactly the union of what you have installed, and its value is discoverability and not memorizing flags. Historical status: last upstream commit 2016; the menu still runs, but several wrapped tools have long since superseded it — treat it as a launcher of convenience rather than a current framework.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Browse and launch attacks from the menu

```
penbox
```

Outcome: opens the category menu (Information Gathering, Password Attacks, Wireless, Exploitation, Web Hacking, Post Exploitation, ...); pick a category, then a tool, and the script prompts for the target details that tool needs and runs it for you — e.g. choosing WordPress User Enumeration under Information Gathering asks for the site and invokes the wrapped tooling. Errors about a missing tool mean its underlying dependency is not installed; install the tool (not PenBox) to enable that entry.
