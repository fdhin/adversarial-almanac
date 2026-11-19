---
name: "sccmhunter"
categories:
  - blackarch-windows
version: "v2.0.0.r78.ge789072"
homepage: "https://github.com/garrettfoster13/sccmhunter"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: windows
license: MIT
meta-checked: "2026-11-09"
github-repo: "garrettfoster13/sccmhunter"
github-stars: 962
github-pushed: "2026-10-28"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 106
gh-activity-checked: "2026-11-09"
---

# sccmhunter

> Post-exploitation console for Microsoft SCCM/MECM: find site systems, profile them, and abuse clients and credentials.

## What it's for

SCCMHunter (by Garrett Foster, `unsigned_sh0rt`) streamlines identifying,
profiling and attacking Microsoft Configuration Manager (SCCM/MECM)
assets from a domain-joined or remote Linux position. It is a single
`python3 sccmhunter.py` console with authentication via password, NT
hash, AES key or Kerberos (impacket-style flags throughout) and one
subcommand per attack surface: `find` locates SCCM site systems in LDAP,
`smb` profiles their shares and hunts credentials (network access
accounts, task-sequence and PXE secrets), `http` abuses client
enrollment against management points — including the client-push-account
takeover — `mssql`/`relay` cover relay paths into SCCM infrastructure,
`admin` runs commands through the AdminService API, and `dpapi` decrypts
SCCM secrets blobs with local-admin access. It was developed and tested
in labs; performance in production environments varies.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Discover SCCM site systems in the domain

Locate the attack surface before doing anything else.

```
sccmhunter find -d example.com -u user -p Password1 -dc-ip 198.51.100.10
```

Outcome: authenticates to LDAP and prints the domain's SCCM site
servers, site databases and management points; `-all` additionally
profiles every computer to find hosts running site system roles
(heavy on large domains), `-resolve` expands nested group membership.

### Profile SCCM servers and hunt credentials over SMB

Work through the shares of discovered site systems.

```
sccmhunter smb -d example.com -u user -p Password1 -dc-ip 198.51.100.10
```

Outcome: enumerates and profiles SMB shares on the SCCM servers found
earlier, hunting for network access account, task-sequence and PXE boot
credentials; add `-save` to write recovered PXE boot variable files to
disk.

### Abuse client enrollment over HTTP

Enroll a fake machine against a management point or push accounts.

```
sccmhunter http -d example.com -u user -p Password1 -dc-ip 198.51.100.10 -auto
```

Outcome: attempts to create a machine account and pull SCCM policies
from a management point with the supplied credentials; `--sccm-push`
targets the client push installation account instead (pair with
`--sccm-push-cn` to set the pushed computer name) and `--altauth`
attacks alternative-authentication configurations.

### Review and export everything collected

Show what earlier modules gathered.

```
sccmhunter show -all -csv
```

Outcome: prints the stored recon tables — site servers, management
points, SCCM-related users, computers, groups and recovered credentials
— or with `-csv`/`-json` exports them for reporting; individual tables
can be selected with flags like `-siteservers`, `-mps` or `-creds`.
