---
name: "spartan"
categories:
  - blackarch-fingerprint
version: "23.babdd7d"
homepage: "https://github.com/sensepost/SPartan"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "sensepost/SPartan"
github-stars: 283
github-pushed: "2021-08-08"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# spartan

> SensePost's SPartan: fingerprints Microsoft SharePoint and FrontPage deployments and then attacks the discovered surface — default file and folder enumeration, Active Directory account enumeration, and authenticated document harvesting.

## What it's for

Reconnaissance and attack against Microsoft SharePoint and FrontPage web deployments, written in Python 2 by Keiran Dennie of SensePost (BlackArch wraps `SPartan.py` as the `spartan` command). It first fingerprints whether the target is SharePoint or FrontPage and which version, copes with IIS "friendly 404" pages that fake every URL as existing, and then enumerates default SharePoint/FrontPage files and directories, lists Active Directory accounts via SharePoint's Search Principals, discovers SharePoint SOAP services, crawls the site, and downloads interesting documents — including uninterpreted ASP/ASPX source that failed to execute. Scans can run authenticated with NTLM credentials (`-l`) or a session cookie (`--cookie`), and state from previous scans is saved. It is not a general-purpose web application scanner: outside SharePoint/FrontPage it has little to offer.

## Typical use cases

### Fingerprint a FrontPage site and crawl it

Establish what FrontPage components a target exposes while mapping its links.

```
spartan -u http://target.example.com -f -c
```

Outcome: fingerprints the FrontPage installation, enumerates default FrontPage files and folders, and crawls linked pages (Ctrl-C stops crawling); found resources are printed by default, with saved state for later runs.

### Fingerprint SharePoint and enumerate AD accounts

Identify a SharePoint deployment and pull user names out of it.

```
spartan -u https://target.example.com -s --users
```

Outcome: performs the SharePoint fingerprint and scans, then lists user accounts found via Search Principals; `--sps` can be added to also discover the SharePoint SOAP services.

### Run an authenticated SharePoint scan and harvest documents

Use captured credentials to enumerate deeper and download leaks.

```
spartan -u https://target.example.com -s -l 'CORP\user:Password1' -d -k
```

Outcome: authenticates to SharePoint with the NTLM credentials, downloads documents of interest (pdf, doc/docx, xls/xlsx, xml, config, csv, stp, webpart — plus ASP/ASPX files being served uninterpreted), and scrapes identified pages for keywords.

### Scan fast with more threads

Speed up enumeration against a responsive target.

```
spartan -u https://target.example.com -s -t 20
```

Outcome: runs the SharePoint scan with up to 20 threads instead of the default 10, finishing large default-file lists quicker; `-i` disables SSL certificate verification for hosts with self-signed certificates.

*No corpus evidence — derived from upstream documentation.*
