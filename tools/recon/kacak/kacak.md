---
name: "kacak"
categories:
  - blackarch-recon
version: "1.0"
homepage: "https://github.com/galkan/kacak"
packaged-by: [blackarch]
blackarch-builddate: "2017-07-22"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "galkan/kacak"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# kacak

> Python 2 toolkit (by Galkan) for Windows-network user hunting: finds where domain users are logged on across a subnet, parses Mimikatz output, and sweeps for MS08-067 — the "road to domain admin" workflow.

## What it's for

Given a list of interesting domain accounts, kacak enumerates which workstations those users are logged on to across an IP range (driving Metasploit's `smb_enum_domain_users`-style queries and nmap from a single command), which is the classic path from any foothold to a Domain Admin's session. It bundles two support modes: parsing Mimikatz credential-dump output for the same user-hunting loop, and an nmap-driven MS08-067 vulnerability sweep of a range. Windows-target-only; depends on Metasploit and python-nmap. The upstream repository (galkan/kacak, 2014) is deleted with no archive — the commands below are verified from a surviving fork's source; BlackArch still packages the tool.

## Typical use cases

### Find where domain users are logged on

```
./kacak.py --domain /data/domain_users.txt /data/config /data/targets.txt -t 20 -o kacak-results.txt
```

Outcome: `--domain` takes three full paths — interesting usernames, a Metasploit config file, and target IPs — then drives the enumeration across all targets (thread count with `-t`), reporting every machine where a listed user has a session to `kacak-results.txt`. Sort the output for Domain Admin logons: those machines are where credential theft ends the hunt.

### Sweep a range for MS08-067

```
./kacak.py --08_067 198.51.100.0/24 -t 20 -o ms08067-sweep.txt
```

Outcome: runs the nmap-based MS08-067 check across the range with 20 threads and writes every vulnerable host to the output file. `-t` and `-o` are mandatory in this mode; useful to pair with the domain mode (a vulnerable host is often the easiest route to a first credential).

### Extract users from Mimikatz output

```
./kacak.py --mimikatz /data/mimikatz-output.txt
```

Outcome: parses a saved Mimikatz credential dump, pulls the harvested accounts, and folds them into the same user-hunting workflow — feeding its output as the users file for `--domain` closes the loop between dumping one host's credentials and finding those users elsewhere.

Add `-v 1` through `-v 3` to any mode for increasing verbosity.

*No corpus evidence — derived from the upstream source code (upstream repository deleted; read from a surviving fork, hotelzululima/kacak).*
