---
name: "nimux"
categories:
  - blackarch-scanner
version: "1.0.6.r2.g12eea77"
homepage: "https://github.com/blue0x1/nimux"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-09-06"
build-system: other
license: AGPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "blue0x1/nimux"
github-stars: 11
github-pushed: "2026-10-22"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 44
gh-activity-checked: "2026-11-09"
---

# nimux

> Pure-Nim native operator toolkit: network scanning, web/DNS discovery, SMB/LDAP/Kerberos AD operations, remote execution, secrets collection and SOCKS pivoting behind one binary.

## What it's for

nimux ("Native Operator Toolkit") packs the enumeration and lateral-movement chores of an authorized network assessment into a single static-ish Nim binary with a consistent subcommand surface: `scan` (TCP/UDP service discovery), `http`/`https`/`dns` (lightweight directory, file, vhost and subdomain discovery), `smb`, `ldap` (AD enumeration, BloodHound-style collection, roasting, RBCD, ADCS), `kerberos` (TGT/TGS/S4U/ticket forge), `winrm`/`scm`/`tsch`/`mmc` (remote execution transports), `secrets`/`dcsync`, `socks` routing, and file helpers (`put`, `get`, `ls`). Nearly everything accepts `--json` for automation, `--proxy socks5://...` to route through a pivot, and a `bh` subcommand analyzes saved BloodHound collections offline. It is offense-oriented tooling for environments you are authorized to test — not a defensive scanner.

## Typical use cases

### Sweep a subnet for AD-relevant services

```
nimux scan 198.51.100.0/24 --port 445,389,5985 --open
```

Outcome: fast TCP scan of the CIDR that prints (or with `--json`, emits) only hosts with the given ports open — the shortlist for SMB/LDAP/WinRM follow-up.

### Discover web paths and vhosts

```
nimux http target.example.com --dirs words.txt --status 200,301,302,403 --workers 100
nimux http target.example.com --files words.txt --extensions php,txt,bak
```

Outcome: wordlist-driven directory and file discovery with status filtering and worker concurrency; `--vhosts`, `--recursion`, `--baseline` and `--filter-regex` refine the same surface.

### Enumerate SMB shares and users with a hash or password

```
nimux smb dc01.example.local -u operator -p 'Password1' -d example.local --shares --users
nimux smb files01.example.local --spider --share Public --max-depth 3 --interesting
```

Outcome: the first command authenticates and lists shares and domain users; the second read-only spiders a share up to a depth, flagging interesting filenames and size-bounded files.

### Collect and analyze BloodHound data

```
nimux ldap dc01.example.local -u operator -p 'Password1' -d example.local --bloodhound --legacy --bloodhound-out collection.zip
nimux bh collection.zip --interesting --graph
```

Outcome: the first collects LDAP/AD data into a BloodHound-compatible ZIP; `bh` then analyzes the saved ZIP fully offline — `--interesting` highlights privileged and stale objects, `--graph` prints relationship paths in the terminal with no GUI or database required.

### Run a command over WinRM

```
nimux winrm host.example.local -u operator -p 'Password1' -d example.local --cmd whoami
```

Outcome: authenticates to the host's WinRM service and returns the command's output; the same auth pattern drives the `scm`, `tsch`, `mmc` and `dcom` execution transports.

### Request Kerberos tickets

```
nimux kerberos dc01.example.local -u operator -p 'Password1' -d example.local --request kinit --out ticket.ccache
```

Outcome: requests a TGT for the principal and writes the ccache file for reuse with other tooling; the `kerberos` family also covers TGS/S4U, kirbi conversion and ticket forging.

### Scan and pivot through SOCKS

```
nimux scan 198.51.100.0/24 --port 445,389,5985 --proxy socks5://127.0.0.1:1080
```

Outcome: the same service sweep, executed through the SOCKS5 pivot (typically the other end of an SSH or C2 tunnel), which keeps second-hop enumeration sourced from the pivot host.

*No corpus evidence — derived from upstream documentation (README quick examples and command-family table of blue0x1/nimux; `/usr/bin/nimux` from the BlackArch PKGBUILD).*
