---
name: "apachetomcatscanner"
categories:
  - blackarch-scanner
version: "3.2"
homepage: "https://github.com/p0dalirius/ApacheTomcatScanner"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: python
license: GPL2
meta-checked: "2026-11-09"
github-repo: "p0dalirius/ApacheTomcatScanner"
github-stars: 893
github-pushed: "2026-01-12"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# apachetomcatscanner

> Multithreaded Apache Tomcat discovery and audit: finds Tomcat instances, fingerprints versions, checks Manager access and lists CVEs.

## What it's for

ApacheTomcatScanner (Podalirius) sweeps targets for Apache Tomcat servers — single
IPs/FQDNs/CIDRs (`-tt`), direct manager URLs (`-tu`), line-by-line target files (`-tf`),
or computers pulled straight from a Windows domain over LDAP (`-ad`/`-ai`/`-au`). For
each instance it fingerprints the version, tests whether `/manager/html` is accessible,
and can spray default or supplied credentials against the Tomcat Manager; with
`--list-cves` it maps each discovered version to its known CVE ids (full descriptions via
`--show-cves-descriptions`) using a bundled NVD database the tool offers to refresh.
Results export to JSON, XLSX or SQLite. It audits Tomcat specifically — it is not a
general-purpose web vulnerability scanner.

## Typical use cases

### Scan a target for Tomcat and list its CVEs

Discovery plus version-to-CVE mapping.

```
ApacheTomcatScanner -tt 198.51.100.10 -tp 8080,8443 -C
```

Outcome: probes the given ports for Tomcat, prints each instance with its version, and
appends the CVE ids affecting that version.

### Sweep a target list with JSON export

Cover a scope and keep structured results.

```
ApacheTomcatScanner -tf targets.txt -T 100 --export-json results.json
```

Outcome: scans every entry of `targets.txt` (IP, DNS or CIDR per line) with 100 threads
and writes instance/version findings to `results.json`.

### Audit Tomcat Manager credentials

Check for the default-credential foothold.

```
ApacheTomcatScanner -tu http://target.example.com:8080 --tomcat-usernames-file users.txt --tomcat-passwords-file passwords.txt
```

Outcome: tests every username/password combination against the Manager at the given URL
and reports valid logins — the check to run before manager WAR-deployment attacks.

### Scan every machine in a Windows domain

Use LDAP as the target source.

```
ApacheTomcatScanner -ad corp.example.com -ai 198.51.100.10 -au user -ap 'Password1' -C
```

Outcome: authenticates to the domain controller, enumerates computer objects (servers only
with `-s`), and scans each for Tomcat with CVE listing enabled — an internal-audit sweep
in one command.

*No corpus evidence — derived from upstream documentation (README usage/help output; installed as `ApacheTomcatScanner` on BlackArch).*
