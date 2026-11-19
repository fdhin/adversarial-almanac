---
name: "smbspider"
categories:
  - blackarch-scanner
version: "10.7db9323"
homepage: "https://github.com/T-S-A/smbspider"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "T-S-A/smbspider"
github-stars: 135
github-pushed: "2021-12-10"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# smbspider

> Lightweight SMB share searcher that hunts filenames and keywords across many hosts, shares, and folders at once.

## What it's for

SMB Spider (python2, born on a pentest that needed to search hundreds of
hosts for password files fast) walks SMB/CIFS/Samba shares and matches
file and folder names against keywords you supply — a single term or a
whole file of them — either anonymously or with credentials. Targets can
be one IP, a CIDR, or a file of addresses, the search can be scoped to a
share and subfolder or recursed to a chosen depth, and the walk can be
threaded. It is a name-matching spider: it reads filenames and paths, not
file contents, so pair it with a content tool once it has found
candidates. The packaged script runs under python2, which is the main
practical constraint on modern use.

## Typical use cases

### Anonymous keyword search in one share

The classic "password files on a public share" hunt.

```
smbspider -ip 198.51.100.10 -s public -pa password
```

Outcome: the null session walks the `public` share from its root and
prints every file and folder whose name contains the keyword; widening is
just more keywords, or a keyword file via `-pf keywords.txt`.

### Credentialed recursive search across a subnet

Sweep many hosts as a domain user.

```
smbspider -ip 198.51.100.0/24 -s 'C$' -f 'Windows/Temp' -u user -p Password1 -d EXAMPLE -pf keywords.txt -r 10 -t 20
```

Outcome: every host in the CIDR is spidered over the admin share starting
at the given subfolder, recursed ten levels, matching any keyword from
the file, with twenty threads keeping the sweep quick; hits print as
host/share/path lines ready for download with a file-transfer tool.

### Pattern-file hunt on a target list

Reuse a keyword corpus across engagements.

```
smbspider -ip hosts.txt -s Documents -pf keywords.txt -r 5
```

Outcome: the targets file is read line by line and each host's named
share is recursed to depth five against the keyword file — the same
sensitive-name list (passwords, config, backup, payroll, ...) applied
consistently to every environment you test.

*No corpus evidence — derived from upstream documentation (README and packaged source).*
