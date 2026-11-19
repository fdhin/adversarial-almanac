---
name: "paranoic"
categories:
  - blackarch-scanner
version: "1.7"
homepage: "http://packetstormsecurity.com/files/128065/Paranoic-Scan.7.html"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: PerlArtistic
meta-checked: "2026-11-09"
github-repo: "Seabreg/paranoic"
github-stars: 0
github-pushed: "2019-03-22"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# paranoic

> Menu-driven Perl web-hacking multi-tool: SQLi, LFI, XSS, CSRF, admin panels and more.

## What it's for

Paranoic Scan (Doddy Hackman, 2014) is an interactive, menu-driven security multi-tool written in Perl for auditing single web applications. Launching it opens a numbered console with 19 modules: Web Scanner, SQLi Scanner, LFI Scanner, XSS Tool, CSRF Tool, Bypass Admin, FSD Exploit Manager, Paths Finder, Locate IP, Crack MD5, Panel Finder, HTTP FingerPrinting, Port Scanner, Encoder & Decoder, Exploit DB Manager, Mysql Manager, Console, Generate LOGS and About. Each module prompts for a target URL and options, and sessions can be exported to HTML logs (module 18). It is a 2014-era point-and-shoot auditor: techniques predate modern WAF/EDR realities, and it has no command-line flags — the source replaced its old option parser with the interactive menu, so it is driven entirely from the console. The packaged binary is `paranoic`; the GitHub repo is a faithful mirror of the same 1.7 script.

## Typical use cases

### Launch the console and pick a module

Start the toolkit against a target site.

```
paranoic
```

Outcome: prints the numbered module menu at an `Option :` prompt; choose e.g. `2` for the SQLi Scanner and enter the target URL when asked, and module output streams to the terminal. Run option 18 afterwards to dump the session's logs for reporting.

No corpus evidence — derived from the packaged source (menu tree and license header of paranoic.pl 1.7).
