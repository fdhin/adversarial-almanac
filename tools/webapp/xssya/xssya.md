---
name: "xssya"
categories:
  - blackarch-webapp
version: "13.cd62817"
homepage: "https://github.com/yehia-mamdouh/XSSYA-V-2.0"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "yehia-mamdouh/XSSYA-V-2.0"
github-stars: 83
github-pushed: "2017-02-03"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xssya

> Cross Site Scripting scanner centered on vulnerability confirmation — it verifies that payloads actually execute, and identifies the WAF in front of the target.

## What it's for

XSSYA (by Yehia Mamdouh; the packaged current version is the XSSYA-V-2.0 repository, which superseded the original XSSYA repo) is a cross-site scripting scanner whose focus is confirmation rather than raw detection. It works in two methods: comparing request/response pairs for reflection, and executing an encoded payload and searching for the same payload decoded inside the returned HTML — which distinguishes executable injections from plain reflections. It ships a library of encoded payloads intended to pass WAFs (plus HTML5 payloads), identifies three WAF types (Mod_Security, WebKnight, F5 BIG-IP), checks for Cross Site Tracing (XST), converts the attacker IP to Hex/Dword/Octal representations to evade input filters, and bundles a small lookup of known XSS-affected versions for Apache, WordPress and phpMyAdmin. Interactive Python tool: run it and it prompts for the target URL (which should end with `/`, `=` or `?`).

## Typical use cases

### Confirm an XSS finding on a target

```
xssya
```

Outcome: the tool prompts for a URL (for example `http://target.example.com/`); it then tries its payload library, reports which injections were confirmed executable, names the WAF detected in front of the site, and checks whether the target answers TRACE requests (XST).

*No corpus evidence — derived from upstream documentation (README of yehia-mamdouh/XSSYA-V-2.0, the repository BlackArch packages).*
