---
name: "xwaf"
categories:
  - blackarch-webapp
version: "162.c6f6bb7"
homepage: "https://github.com/3xp10it/xwaf"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "3xp10it/xwaf"
github-stars: 990
github-pushed: "2022-03-09"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xwaf

> Automated WAF-bypass driver that wraps sqlmap and brute-forces tamper-script combinations until the WAF is beaten.

## What it's for

xwaf (by 3xp10it; the repository was renamed from `bypass_waf` to `xwaf`) automates defeating a web application firewall in front of a SQL injection point. It drives sqlmap through a pipeline — confirm the injection, fingerprint the database type, pick efficient injection techniques, then enumerate database, tables, columns and data — trying permutations of sqlmap tamper scripts (combined with `--hex`/`--no-cast`) at each step until one combination passes the WAF. It accepts almost all sqlmap arguments (`-u`, `-r`, `--data`, `-p`, `--risk`, `--level`, base `--tamper`, ...; only `-m`/`-l` batching is unsupported), runs unattended, resumes from where a previous run stopped, and writes the winning command to a `bypassed_command` entry in an ini file under `/root/.sqlmap/output/<host>/`. It needs sqlmap and a MySQL server, and it targets SQLi/WAF scenarios — it is not an XSS or general web scanner despite the historical name neighborhood.

## Typical use cases

### Attack a GET injection point

```
xwaf -u "http://target.example.com/page.php?id=1" -p id
```

Outcome: xwaf verifies the injection behind the WAF, then iterates tamper permutations step by step; when data extraction succeeds, the working sqlmap command is stored as `bypassed_command` in the target's ini file under `/root/.sqlmap/output/target.example.com/`.

### Work from a captured request

```
xwaf -r request.txt -p id --risk 3 --level 5
```

Outcome: the raw request file is replayed the sqlmap way (`-r`), with the supplied `--risk`/`--level` values layered on top of xwaf's own parameter scheme; user-supplied options take priority over the built-in defaults.

### Attack a POST parameter

```
xwaf -u "http://target.example.com/page.php" --data="id=1" -p id
```

Outcome: the same automated tamper brute-force pipeline runs against the POST body instead of the query string.

### Seed the search with a known tamper

```
xwaf -u "http://target.example.com/page.php?id=1" --tamper=space2comment
```

Outcome: every tamper combination xwaf tries is built on top of `space2comment`, narrowing the search when one bypass component is already known.

*No corpus evidence — derived from upstream documentation (readme.md of 3xp10it/xwaf, formerly bypass_waf).*
