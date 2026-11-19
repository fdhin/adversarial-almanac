---
name: "nbtscan"
categories:
  - information-gathering
version: "1.7.2-2"
homepage: "https://github.com/resurrecting-open-source-projects/nbtscan"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2024-07-12"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "resurrecting-open-source-projects/nbtscan"
github-stars: 51
github-pushed: "2022-10-29"
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

# nbtscan

> Sweeps IP ranges for NetBIOS names, logged-in users and MAC addresses.

## What it's for

NBTscan (originally by Alla Bezroutchko, now maintained by the resurrecting-open-source-projects group) sends NetBIOS name-service status queries to every address in a range and tabulates the replies: IP address, NetBIOS computer name, whether the host shares files/printers, the logged-in username, and the MAC address. On Windows-heavy and legacy industrial networks it remains a fast way to inventory hosts and current users without credentials; on networks where SMBv1/NetBIOS is disabled there is simply nothing to see. It is a query tool — it reads name tables, it does not authenticate or exploit. Targets are single IPs, CIDRs, or `a.b.c.d-a.b.c.e` ranges, and `-f` reads targets from a file. No corpus evidence — derived from upstream documentation.

## Typical use cases

### Sweep a subnet for NetBIOS hosts

Inventory a range in one pass.

```
nbtscan 198.51.100.0/24
```

Outcome: sends a NetBIOS status query to each address and prints a columned table — IP, NetBIOS name, server/share flag, logged-in user, MAC — one row per responding host. Use `-r` (root required) to source queries from UDP port 137, which some legacy Windows 95-era stacks answer.

### Dump a host's full name table

See every NetBIOS name a machine registers.

```
nbtscan -v -h 198.51.100.10
```

Outcome: verbose mode prints the complete NetBIOS name table for the host (workgroup, messenger names, browser and domain entries with their <00>/<20>/<1d> service codes) with `-h` translating service codes into human-readable names — the workgroup/domain name and logon names live here.

### Script-friendly output for parsing

Feed results into other tooling.

```
nbtscan -s : 198.51.100.0/24
```

Outcome: drops the column headers and emits one `ip:name:server:user:mac` line per host with `:` as field separator (any character can follow `-s`), ready for `cut`, `awk` or CSV import.

### Scan a target list from a file

Work from a prepared address list.

```
nbtscan -f targets.txt
```

Outcome: reads addresses (one per line, any of the accepted target forms) from `targets.txt` and scans them all — useful when the target set comes from a previous discovery step rather than a contiguous range. Tune slow links with `-b <bps>` throttling and `-t <seconds>` response timeout.
