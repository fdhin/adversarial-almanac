---
name: "hekatomb"
categories:
  - blackarch-windows
version: "107.bdd53cf"
homepage: "https://github.com/ProcessusT/HEKATOMB"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: windows
license: GPL3
meta-checked: "2026-11-09"
github-repo: "ProcessusT/HEKATOMB"
github-stars: 518
github-pushed: "2024-07-31"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hekatomb

> Domain-wide sweep that collects and decrypts every user's DPAPI secrets from all domain machines.

## What it's for

HEKATOMB (ProcessusT) chains three steps into one command: it queries LDAP on the domain controller to enumerate all computers and users, reaches out over SMB to every reachable machine to pull each user's DPAPI blobs and masterkey files, then extracts the domain controller's DPAPI private key over RPC and uses it to decrypt everything — recovering the secrets saved in every user's Windows Credential Manager across the whole domain. Authentication follows the impacket convention (domain/user:password or NTLM hashes against the DC), and it needs Domain Admin-equivalent rights but exploits nothing. Use it in authorized Active Directory assessments to show how far one compromised privileged account reaches via DPAPI; it does not attack machines that are offline or unreachable over SMB.

## Typical use cases

### Harvest all DPAPI secrets in a domain

One command, whole-domain credential recovery.

```
hekatomb 'corp.example.com/user:Password1@198.51.100.10'
```

Outcome: enumerates computers and users from the DC via LDAP, scans each computer for open SMB, downloads every user's DPAPI blobs and masterkeys, fetches the DC private key through RPC, and prints the decrypted Credential Manager secrets grouped by computer and by user.

### Run with pass-the-hash

Reuse an NTLM hash instead of a password.

```
hekatomb -hashes :NTHASH corp.example.com/user@198.51.100.10
```

Outcome: same full sweep, authenticating with the NT hash in impacket's LM:NTHASH form (empty LM half); useful when you only hold hashes from a prior dump.

### Limit the run to one user or one computer

Scope the collection when testing reachability.

```
hekatomb -just-user user -just-computer WORKSTATION-01 corp.example.com/user:Password1@198.51.100.10
```

Outcome: only the named user's profile folders and the named computer are queried over SMB, so the run is fast and quiet before committing to the full sweep.

### Supply a pre-extracted domain backup key

Skip the RPC private-key extraction step.

```
hekatomb -pvk domain-backup.pvk corp.example.com/user:Password1@198.51.100.10
```

Outcome: uses the supplied domain backup key file (e.g. one dumped earlier with the usual impacket/backuptooling) instead of retrieving the key from the DC, and proceeds straight to blob collection and decryption.

### Export results and mask passwords

Produce a shareable report without cleartext credentials.

```
hekatomb -csv -md5 corp.example.com/user:Password1@198.51.100.10
```

Outcome: writes the harvested secrets to CSV and prints MD5 hashes of passwords instead of cleartext, suitable for attaching to a findings report.

*No corpus evidence — derived from upstream documentation.*
