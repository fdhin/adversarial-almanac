---
name: "adrecon"
categories:
  - recon
version: "git (2020-06-15)"
homepage: "https://github.com/adrecon/ADRecon"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "adrecon/ADRecon"
github-stars: 992
github-pushed: "2024-10-15"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 14
---

# adrecon

> PowerShell script that gathers a holistic picture of an Active Directory domain — users, trusts, ACLs, GPOs, DNS, LAPS and more — into an Excel report with summary metrics.

## What it's for

ADRecon is a Windows PowerShell tool that extracts a wide sweep of artefacts from an AD environment: forest and domain data, trusts, sites, subnets, password policies, domain controllers and FSMO roles, users and their attributes, SPNs, groups, OUs, GPOs and gPLinks, DNS zones and records, printers, computers, LAPS passwords, BitLocker recovery keys, and ACLs for domain/OU/GPO/user/computer/group objects. It runs from any workstation connected to the environment — domain member or not — in the context of a standard domain user (only fine-grained password policies, LAPS and BitLocker retrieval typically need more privilege). It uses RSAT/ADWS when available and falls back to raw LDAP otherwise. Output is a folder of CSVs plus, when Excel is installed, a formatted `ADRecon-Report.xlsx` with summary views and metrics — aimed at auditors, DFIR and assessors who need the current state of a domain in one document. It is a collector; it performs no attacks.

## Typical use cases

### Run on a domain-joined host

Collect everything with the current user's context.

```
PS C:\> .\ADRecon.ps1
```

Outcome: enumerates the domain via ADWS (RSAT) using your logged-on credentials and creates an `ADRecon-Report-<timestamp>` folder containing the CSV files and — if Excel is installed — the formatted `ADRecon-Report.xlsx` with summary sheets and metrics.

### Run as a specific user against a chosen DC

Target a domain controller explicitly with alternate credentials.

```
PS C:\> .\ADRecon.ps1 -DomainController dc01.example.com -Credential example\user
```

Outcome: prompts for the password of the given account, binds to the named DC, and produces the same report folder; collection scope follows that account's read rights in the directory.

### Run from a non-domain-joined host over LDAP

Enumerate from a standalone workstation without RSAT installed.

```
PS C:\> .\ADRecon.ps1 -Protocol LDAP -DomainController dc01.example.com -Credential example\user
```

Outcome: falls back to talking LDAP directly to the DC (no ADWS/RSAT requirement), producing the full CSV/Excel output — useful from an assessment VM that is not a domain member.

### Collect only selected modules

Trim the run to the artefacts you actually need.

```
PS C:\> .\ADRecon.ps1 -Collect Domain,DomainControllers,Trusts -DomainController dc01.example.com -Credential example\user
```

Outcome: only the named modules execute (valid values include Forest, Domain, Trusts, Users, Groups, ACLs, GPOs, DNSZones, Computers, LAPS, Kerberoast, ...), which cuts runtime and output size; results print to STDOUT by default when `-Collect` is used — add `-OutputType CSV,Excel` to keep files.

### Generate the Excel report without Excel on the collector

Produce the workbook from CSVs collected earlier on a host without Office.

```
PS C:\> .\ADRecon.ps1 -GenExcel C:\ADRecon-Report-20260101120000
```

Outcome: reads the CSV files in the named ADRecon output folder and builds `ADRecon-Report.xlsx` on this machine, so the formatted report can be assembled on an analyst workstation after collection elsewhere.

*No corpus evidence — derived from upstream documentation.*
