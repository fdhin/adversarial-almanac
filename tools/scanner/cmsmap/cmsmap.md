---
name: "cmsmap"
categories:
  - blackarch-scanner
version: "8.59dd0e2"
homepage: "https://www.dionach.com/blog/cmsmap-%E2%80%93-a-simple-cms-vulnerability-scanner"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Dionach/CMSmap"
github-stars: 1184
github-pushed: "2021-12-01"
github-archived: false
github-issues: 32
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cmsmap

> CMS vulnerability scanner for WordPress, Joomla, Drupal and Moodle.

## What it's for

CMSmap (Dionach, author Mike Manzotti) fingerprints a target site and automates the detection of security flaws in the most popular CMSs — WordPress, Joomla, Drupal and Moodle — integrating the common vulnerabilities of each into a single tool. For WordPress it enumerates plugins and themes and matches them against an Exploit-DB database (the `edbtype`/`edbpath` settings in cmsmap.conf point at your Exploit-DB copy; the BlackArch package configures the packaged exploitdb tree). It also runs low-intensity dictionary attacks against CMS logins during scans and can crack WordPress/Joomla password hashes post-exploitation by shelling out to hashcat. An early-stage project by its own README — expect false positives, especially in full-scan mode.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Detect and scan a CMS

Let the tool fingerprint the target, then audit it.

```
cmsmap https://example.com
```

Outcome: identifies the CMS and version, enumerates plugins/themes and users where the CMS allows, and lists matched known vulnerabilities from the configured Exploit-DB copy.

### Force a CMS and go deep

Full scan with the large plugin lists.

```
cmsmap https://example.com -f W -F --noedb -d
```

Outcome: treats the target as WordPress (`-f J` Joomla, `-f D` Drupal), enumerates the large plugin list (slower, more false positives), skips Exploit-DB lookups and runs a low-intensity dictionary attack against discovered users.

### Scan many targets

Batch a list of sites.

```
cmsmap -i targets.txt -o output.txt
```

Outcome: each URL in targets.txt is scanned in turn and all findings are saved to output.txt.

### Brute-force a login

Try credentials against the CMS login.

```
cmsmap https://example.com -u admin -p passwords.txt
```

Outcome: attempts the passwords in the file for user admin (about 5 attempts per user by default; WordPress brute forcing goes through XML-RPC unless `--noxmlrpc` is given).

### Crack CMS password hashes

Post-exploitation hash cracking through hashcat.

```
cmsmap -k hashes.txt -w passwords.txt
```

Outcome: feeds the hash file and wordlist to hashcat (WordPress and Joomla hashes only; hashcat must be installed) and reports recoverable passwords.
