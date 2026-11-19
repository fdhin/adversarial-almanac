---
name: "iis-shortname-scanner"
categories:
  - blackarch-scanner
version: "5.4ad4937"
homepage: "https://github.com/lijiejie/IIS_shortname_Scanner"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "lijiejie/IIS_shortname_Scanner"
github-stars: 600
github-pushed: "2022-12-08"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# iis-shortname-scanner

> Brute-force enumerator of Windows 8.3 short names (e.g. `SHORTN~1`) on IIS servers with .NET enabled, via the classic `*~1*` tilde disclosure — turning a blind file system into a partial directory listing.

*Usage grounded in corpus command-line evidence (jar invocation); current CLI verified against the upstream repository, whose master ships the Python scanner.*

## What it's for

lijiejie's IIS shortname scanner exploits the IIS tilde-directory enumeration weakness: on affected IIS/.NET configurations, requests such as `http://target/*~1*/a.aspx` and `http://target/l1j1e*~1****/a.aspx` return distinguishably different responses (typically 404 vs non-404) depending on whether a matching short name exists. The tool walks that oracle character by character to recover `NAME~1` short names for otherwise-hidden files and directories. Short names leak the first six characters of every file and folder name — often enough to find backups, config files or admin paths, and to feed dedicated shortname-to-longname guessing. It is detection/enumeration only; it cannot read file contents. Older releases shipped a Java variant (`iis_shortname_scanner.jar`, invoked with `java -jar iis_shortname_scanner.jar <threads> <wait-ms> <url>`); the current repository installs the Python scanner, which BlackArch exposes as `iis-shortname-scanner`.

## Typical use cases

### Enumerate short names on a suspect IIS host

```
iis-shortname-scanner http://target.example.com/
```

Outcome: Probes the tilde oracle character by character and prints each discovered short name as it is resolved (e.g. `ASPNETC~1`, `SECRET~1`), ending with the collected list of file and directory short names — each is a lead for full-name guessing or direct requests. Run it against a path you are authorized to test; the probe is a stream of easily-logged requests.

### Run the upstream Python scanner directly

```
python iis_shortname_scan.py http://target.example.com/
```

Outcome: Same enumeration from a checkout of the repository without the packaged wrapper — identical single-argument interface (the target URL, trailing slash included).
