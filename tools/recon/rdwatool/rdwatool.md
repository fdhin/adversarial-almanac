---
name: "rdwatool"
categories:
  - blackarch-recon
version: "1.2.r15.g75e6267"
homepage: "https://github.com/p0dalirius/RDWAtool"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: python
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "p0dalirius/RDWAtool"
github-stars: 122
github-pushed: "2026-01-05"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# rdwatool

> Extract, spray and bruteforce against a Microsoft Remote Desktop Web Access (RDWA) login page.

## What it's for

RDWAtool is a Python all-in-one CLI for Microsoft Remote Desktop Web Access applications. Its `recon` mode parses the pre-filled hidden fields of the `RDWeb/Pages/.../login.aspx` page to recover the remote server's FQDN (and from it the internal AD domain name), and fingerprints the Windows Server version by hashing the default OS-banner image (`/RDWeb/Pages/images/WS_h_c.png`) against a known table of 2008 R2–2022 banners. The `spray` and `brute` modes use the same login form for password spraying and per-user bruteforce. It is aimed at the reconnaissance-to-credential-attack bridge against exposed RD Web Access; it does not touch the RDP protocol itself.

## Typical use cases

### Recon a single RD Web Access instance

```
rdwatool recon -tu https://rds.example.com/RDWeb/Pages/en-US/login.aspx
```

Outcome: fetches and parses the login page, printing the remote server FQDN, the derived internal AD domain name and the detected Windows Server version for that RDWA endpoint.

### Sweep a list of RDWA URLs and export results

```
rdwatool recon -tf rdwa-targets.txt --export-json rdwa-results.json
```

Outcome: runs the same extraction over every URL in `rdwa-targets.txt` (250 threads by default) and writes the combined findings to `rdwa-results.json` (`--export-xlsx` and `--export-sqlite` produce spreadsheet and database outputs instead).

### Password spray an RDWA login form

```
rdwatool spray -tu https://rds.example.com/RDWeb/Pages/en-US/login.aspx
```

Outcome: authenticates against the login page with one password across many users (or one user across many passwords), reporting which attempts succeeded — valid web credentials that usually carry over to the RDP service behind the gateway.

### Bruteforce a single RDWA account

```
rdwatool brute -tu https://rds.example.com/RDWeb/Pages/en-US/login.aspx
```

Outcome: iterates a password wordlist against one account on the login form and stops to report the first valid credential pair it finds.

*No corpus evidence — derived from upstream documentation.*
