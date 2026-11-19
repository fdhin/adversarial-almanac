---
name: "taipan"
categories:
  - blackarch-scanner
version: "2.9.498.18"
homepage: "https://github.com/enkomio/Taipan"
packaged-by: [blackarch, aur]
blackarch-builddate: "2022-03-19"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "enkomio/Taipan"
github-stars: 460
github-pushed: "2021-05-07"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# taipan

> Automated web application vulnerability scanner (Community Edition) running on .NET/Mono.

## What it's for

Taipan is Enkomio's automated web application vulnerability scanner: point it at a URL and it crawls the site, fingerprints the stack and runs its vulnerability checks without manual test crafting. Scans are driven by profiles that switch feature groups on or off, and the Community Edition carries the same code base as the commercial editions minus the advanced features — free for non-commercial use only. It runs natively on Windows (.NET) and on Linux through mono; BlackArch packages the release binary behind a `taipan` launcher that executes `mono Taipan.exe`. It is a batch scanner, not an intercepting proxy for manual testing.

## Typical use cases

### Launch a profile-driven scan

```bash
taipan -p Full -u http://target.example.com/
```

Outcome: Taipan crawls the target and executes the selected profile's scan workflow (crawler, vulnerability checks), logging each scan step to the console; the profile name accepts a unique prefix, so `Full` selects the full-checks profile.

### Drive the crawler with a specific Chrome installation

```bash
taipan -p Full -u http://target.example.com/ --chrome-binary /usr/bin/chromium --chrome-driver-binary /usr/bin/chromedriver
```

Outcome: the crawler uses the supplied browser binary with its matching Selenium driver instead of its built-in HTTP fetching — needed for JavaScript-heavy targets — and `--save-pages` additionally persists the crawled pages for review.

*No corpus evidence — derived from upstream documentation (enkomio/Taipan README; scan invocation `-p <profile> -u <url>` per the project README's usage section as archived by third-party quotes of the pre-2021 text; `--chrome-binary`, `--chrome-driver-binary`, `--save-pages` argument names extracted from the packaged ES.Taipan.Application.dll; BlackArch PKGBUILD wraps `mono Taipan.exe` as `taipan`).*
