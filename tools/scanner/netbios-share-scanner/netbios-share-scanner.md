---
name: "netbios-share-scanner"
categories:
  - blackarch-scanner
version: "1.0"
homepage: "http://www.secpoint.com/netbios-share-scanner.html"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# netbios-share-scanner

> Check Windows and Samba hosts for network shares that can actually be listed — as Guest or as a supplied user.

## What it's for

SecPoint's NetBIOS Shares Scanner (Python 2) wraps smbclient: give it an IP, a CIDR or a plain address range and it logs into each Windows/Samba host — Guest access by default, or any `user%password` pair you pass — enumerates the shared disks, and attempts a directory listing of each share's root. "Accessible" therefore means readable, not merely visible in the browse list. The report is plain text per host, so redirection to a file is the normal way to keep results. It drives smbclient under the hood, so smbclient must be installed, and the script must run from its own directory because it imports bundled helper modules (ipaddr, iprange).

No corpus evidence — derived from the packaged upstream release (README and script).

## Typical use cases

### Check one host for open shares

Probe a single workstation or server.

```
cd /usr/share/netbios-share-scanner
python2 netbios-shares-scanner.py 198.51.100.10
```

Outcome: logs in as Guest, prints every shared disk found on the host, and shows a directory listing for each share that permits it.

### Sweep a subnet for readable shares

Cover a whole network range and keep the report.

```
cd /usr/share/netbios-share-scanner
python2 netbios-shares-scanner.py 198.51.100.0/24 > shares.txt
```

Outcome: scans every address in the CIDR (plain `198.51.100.1-198.51.100.254` ranges also accepted), writing the per-host share report to shares.txt; use `>>` to append across multiple runs.

### Test share visibility as a specific user

See what a real account can reach instead of Guest.

```
cd /usr/share/netbios-share-scanner
python2 netbios-shares-scanner.py 198.51.100.0/24 user%Password1
```

Outcome: repeats the sweep authenticating as `user`, so the report reflects that account's share access — useful for validating that a low-privilege user cannot list administrative shares.
