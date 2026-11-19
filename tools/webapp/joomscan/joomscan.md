---
name: "joomscan"
categories:
  - blackarch-webapp
version: "83.2ea8cc7"
homepage: "http://joomscan.sourceforge.net/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL3
meta-checked: "2026-11-09"
github-repo: "OWASP/joomscan"
github-stars: 1196
github-pushed: "2024-09-11"
github-archived: false
github-issues: 24
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# joomscan

> OWASP Joomla! Vulnerability Scanner: version and component enumeration with vulnerability matching.

## What it's for

OWASP JoomScan is a Perl scanner for Joomla CMS deployments and the maintained flagship of this vault's Joomla family (the legacy joomlascan RFI prober, the archived joomlavs, the deleted-upstream jooforce brute forcer, and the jomplug fingerprinter cover narrower slices). Given a URL it fingerprints the Joomla version, matches that version against its vulnerability database, enumerates installed components (1209 most-popular by default, expandable with `-ec`), checks those components against 1000+ known exploits, and also looks for firewall presence, common log files and backup files — then writes text and HTML reports. It is detection-only: it reports what is vulnerable, it does not exploit.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Run the default scan

Version detection plus the default component checks.

```
joomscan -u https://target.example.com
```

Outcome: prints the detected Joomla version with matching core vulnerabilities, default component enumeration results, firewall/log/backup findings, and writes text and HTML reports into its reports directory.

### Enumerate the full component list

Deep-enumerate components beyond the popular default set.

```
joomscan -u https://target.example.com -ec
```

Outcome: `--enumerate-components` expands enumeration to the full component list, and each component found is checked against the exploit database, materially lengthening the scan and the report.

### Scan stealthily through a proxy

Randomize the user-agent and route through a local proxy.

```
joomscan -u https://target.example.com -r --proxy http://127.0.0.1:8080
```

Outcome: each request uses a random user-agent and traverses the proxy — handy for reviewing the scan in Burp or for not advertising one fixed client string; `--cookie "test=demo;"` and `-a "<user-agent>"` are the fixed-cookie and fixed-agent alternatives.

### Refresh the scanner between engagements

Pull the latest vulnerability data before scanning.

```
joomscan --update
```

Outcome: updates the scanner to the latest version from upstream — run it when reports start missing recent Joomla advisories, since the packaged copy's databases age with the package.
