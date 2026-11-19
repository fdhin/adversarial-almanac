---
name: "payloadsallthethings"
categories:
  - blackarch-misc
version: "4.2.r116.g3ac2790"
homepage: "https://github.com/swisskyrepo/PayloadsAllTheThings/"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "swisskyrepo/PayloadsAllTheThings"
github-stars: 82456
github-pushed: "2026-10-30"
github-archived: false
github-issues: 41
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 78
gh-activity-checked: "2026-11-09"
---

# payloadsallthethings

> Crowdsourced playbook of payloads and bypasses for web application testing — one directory per technique.

## What it's for

PayloadsAllTheThings is a curated reference collection, not a program: for each class of web vulnerability (SQL injection, server-side template injection, insecure file upload, XSS, CSRF and seventy-odd more) it provides a methodology README with working payloads, ready-to-load wordlists for Burp Intruder, and supporting proof-of-concept files. Reach for it once you know the bug class and need concrete payload strings — filter bypasses, encoding tricks, per-technology variants — rather than a scanner or an automated exploiter. On BlackArch the whole collection is installed under `/usr/share/payloadsallthethings` so it works offline; the same content is browsable as a website and as raw files straight from the repository when the tool is not installed.

## Typical use cases

### Survey which techniques are covered

```
ls /usr/share/payloadsallthethings/
```

Outcome: one directory per vulnerability class (Command Injection, Upload Insecure Files, Server Side Template Injection, ...) plus methodology pages — pick the one matching the finding.

### Read the playbook for one technique

```
less '/usr/share/payloadsallthethings/Upload Insecure Files/README.md'
```

Outcome: the methodology and payload list for that bug class, including upload-filter bypasses and per-technology tricks such as extension and content-type manipulation.

### Grep the whole library for a payload fragment

```
grep -ril 'content-type' /usr/share/payloadsallthethings/ | head
```

Outcome: lists every file mentioning the fragment, so the relevant technique page can be opened directly instead of browsed for.

### Feed a shipped wordlist to a fuzzer

```
ffuf -u https://target.example.com/FUZZ -w '/usr/share/payloadsallthethings/SQL Injection/Intruder/FUZZDB_MYSQL.txt'
```

Outcome: fuzzes the target URL with the repository's Intruder wordlist for MySQL injection strings — the Intruder directories exist precisely to be loaded into Burp Intruder or command-line fuzzers.
