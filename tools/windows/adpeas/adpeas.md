---
name: "adpeas"
categories:
  - blackarch-windows
version: "1.3.0.r15.g0bfcedc"
homepage: "https://github.com/ajm4n/adpeas"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-15"
build-system: windows
license: MIT
meta-checked: "2026-11-09"
github-repo: "ajm4n/adpeas"
github-stars: 179
github-pushed: "2025-04-01"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# adpeas

> winPEAS, but for Active Directory — one low-privileged credential runs a whole battery of AD enumeration checks and orchestrates the standard tooling around it.

## What it's for

Automating the first hour of an internal AD assessment. adPEAS takes a single set of low-privileged domain credentials plus a DC address and runs the checks an operator would otherwise chain by hand: kerberoastable accounts, delegation misconfigurations, user and password-policy enumeration, LDAP/SMB signing requirements, ZeroLogon and noPAC exposure, and WebDAV/SMB-share sweeps across a scope file. It also shells out to the heavyweights: `certipy find -enabled -vulnerable` and certi for AD CS enumeration, and a full bloodhound-python collection for import into BloodHound. Those external tools (certipy, certi, netexec/nxc, impacket, bloodhound-python) must be installed — adPEAS is the orchestrator, not the implementation. It reports and collects; exploitation stays manual. It runs from Linux against the domain — nothing executes on the Windows hosts themselves.

*No corpus evidence — derived from upstream documentation (README and adPEAS source).*

## Typical use cases

### Point it at a domain

```
adPEAS -u user -p 'Password1' -d example.com -i 198.51.100.10
```

Outcome: runs the full battery section by section — kerberoast harvest, BloodHound ingest collection, certipy/certi AD CS findings, delegation, users, password policy, signing checks, ZeroLogon/noPAC probes — printing results to the terminal and leaving the BloodHound JSON ingest files and certipy's report in the working directory. Omit `-p` to be prompted for the password.

### Save each section to its own file

```
adPEAS -u user -p 'Password1' -d example.com -i 198.51.100.10 -s scope.txt --save yes
```

Outcome: same battery, but every section is written to a file in the working directory — `kerberoast.txt`, `certi.txt`, `delegation.txt`, `users.txt`, `password_policy.txt`, `ldap_signing.txt`, `zerologon.txt`, `nopac.txt`, `webdav.txt`, `smb_signing.txt`, `smb_shares.txt` — and hosts with SMB signing disabled are listed in `relayme.txt` as ready relay targets. `-s scope.txt` supplies the newline-delimited host list the share/signing/WebDAV sweeps require; `--no-bloodhound` and `--no-certipy` skip those collectors.
