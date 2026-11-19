---
name: "webpwn3r"
categories:
  - blackarch-scanner
version: "38.3d75e76"
homepage: "https://github.com/zigoo0/webpwn3r"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "zigoo0/webpwn3r"
github-stars: 460
github-pushed: "2021-12-09"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# webpwn3r

> Interactive Python 2 web-application scanner for RCE, code injection, SQLi and XSS in a single URL or a list of URLs.

## What it's for

WebPwn3r (by Ebrahim Hegazy, @Zigoo0) is a 2015-era point-and-shoot web vulnerability scanner: you give it URLs that already contain query parameters and it fires an adapted payload set at each one, reporting remote code injection, remote command execution, SQL injection and typical XSS findings, plus a WebKnight WAF check and a fingerprint of the backend technology. Its payloads were tuned to survive common filters of that era. The tool is unmaintained, Python 2.7-only, and fully menu-driven — there are no command-line flags. Reach for it only for quick legacy triage of parameterized URLs; modern maintained alternatives will do everything it does with better coverage.

## Typical use cases

### Scan a single parameterized URL

```
python scan.py
```

Outcome: the menu asks URL (1) or list (2); pick `1` and paste a full URL including its query string, e.g. `http://target.example.com/news.php?com=val&id=11&page=24` — the scanner runs its payload set against each parameter and prints per-parameter findings (RCE/code injection/SQLi/XSS), WAF detection and backend fingerprint.

### Scan a prepared list of URLs

```
python scan.py
```

Outcome: pick `2` at the prompt and point it at your file of full parameterized URLs — each entry is scanned in turn with the same checks, so a whole crawled endpoint list can be triaged in one session.

*No corpus evidence — derived from upstream documentation.*
