---
name: "donpapi"
categories:
  - blackarch-windows
version: "V1.2.0.r41.g4f01e2b"
homepage: "https://github.com/login-securite/DonPAPI"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-09-13"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "login-securite/DonPAPI"
github-stars: 1435
github-pushed: "2026-09-09"
github-archived: false
github-issues: 23
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# donpapi

> One-command sweep of DPAPI-protected secrets across Windows hosts.

## What it's for

DonPAPI (Login Sécurité) remotely harvests everything protected by Windows DPAPI from a list of machines, in one authenticated sweep: Chromium and Firefox passwords, cookies and refresh tokens, Credential Manager entries, WiFi keys, certificates with private keys, VNC/mRemoteNG/MobaXterm/RDCMan configurations, SCCM secrets, vaults and user files. It authenticates impacket-style (password, NTLM hash, Kerberos ticket or LAPS), avoids on-disk artifacts that trigger AV, and can fetch the domain DPAPI backup key so masterkeys can be decrypted offline. It exploits nothing — it needs valid credentials — and its loot lands in a local SQLite database you browse with the bundled web GUI.

## Typical use cases

### Sweep a domain with a password

Collect every secret reachable by one account.

```
donpapi collect -u user -p 'Password1' -d domain.local -t ALL --fetch-pvk
```

Outcome: authenticates across the domain's computers (`-t ALL` resolves every machine via LDAP), runs all collectors, stores the harvested secrets under `~/.donpapi/loot/`, and `--fetch-pvk` additionally dumps the domain backup key for offline masterkey decryption.

### Target a subnet with hash authentication

Reuse an NTLM hash instead of a password.

```
donpapi collect -u user -H LMHASH:NTHASH -d domain.local --dc-ip 198.51.100.10 -t 198.51.100.0/24
```

Outcome: sweeps the given CIDR (targets accept IPs, ranges, hostnames, FQDNs or a file of targets) with pass-the-hash style credentials; loot goes into the same database.

### Collect a single secret type

Limit the run to specific collectors.

```
donpapi collect -u user -p 'Password1' -d domain.local -t 198.51.100.10 -c Wifi,CredMan
```

Outcome: runs only the named collectors (Chromium, Certificates, CredMan, Files, Firefox, MobaXterm, MRemoteNG, RDCMan, SCCM, Vaults, VNC, Wifi) instead of the default All, which is quieter and faster when you are after one credential class.

### Browse collected secrets

Explore the loot database.

```
donpapi gui
```

Outcome: spawns a local Flask webserver that crawls the DonPAPI database so you can search and filter harvested credentials per host and user.

*No corpus evidence — derived from upstream documentation.*
