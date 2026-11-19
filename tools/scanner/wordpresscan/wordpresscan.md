---
name: "wordpresscan"
categories:
  - blackarch-scanner
version: "76.f810c1c"
homepage: "https://github.com/swisskyrepo/Wordpresscan"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "swisskyrepo/Wordpresscan"
github-stars: 653
github-pushed: "2021-06-04"
github-archived: true
github-issues: 20
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wordpresscan

> Python reimplementation of WPScan (with WPSeku ideas) for enumerating and brute-forcing WordPress sites.

## What it's for

Wordpresscan (Swissky, archived 2021) covers the standard WordPress audit loop: version disclosure (readme.html, meta generators), theme and plugin enumeration with vulnerability lookup against the WPScan vulnerability database, user enumeration, aggressive file fuzzing, and password brute forcing with per-user or listed usernames. It was written as a Python 2 port of the Ruby WPScan workflow plus ideas from WPSeku, so flag names follow that lineage. The WPScan database it updates against has moved on since the project was archived — run `--update` and treat plugin-vulnerability matches as leads to verify, not ground truth.

## Typical use cases

### Refresh the vulnerability database and run a standard scan

```
python2 wordpresscan.py -u http://target.example.com/wordpress --update --random-agent
```

Outcome: updates the local WPScan-derived vulnerability data, then enumerates the WordPress version, themes and plugins and reports known-vulnerable components; `--random-agent` rotates the user agent for the session.

### Enumerate plugins and themes aggressively

```
python2 wordpresscan.py -u http://target.example.com/wordpress --aggressive
```

Outcome: switches enumeration to the aggressive mode that probes for plugins and themes by name instead of relying on passive markers — more requests, more findings.

### Fuzz for hidden files and paths

```
python2 wordpresscan.py -u http://target.example.com/wordpress --fuzz
```

Outcome: dictionary-fuzzes the site for files, directories, themes and plugins, printing each hit (e.g. `license.txt`, `readme.html`, `wp-admin/admin-ajax.php`) as it is found — the noisiest mode, mostly not advised against hardened targets.

### Brute force logins

```
python2 wordpresscan.py -u http://target.example.com/wordpress --brute --users-list fuzz/users.txt --passwords-list fuzz/wordlist.lst --nocheck
```

Outcome: first enumerates users (or takes them from `--users-list`; `--usernames "admin,guest"` supplies them inline), then attempts every password in the list per user with a progress bar; `--nocheck` skips the pre-scan checks and goes straight to brute forcing. Successes print as valid credential pairs.

*No corpus evidence — derived from upstream documentation.*
