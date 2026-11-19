---
name: "smbcrunch"
categories:
  - blackarch-recon
version: "12.313400e"
homepage: "https://github.com/Raikia/SMBCrunch"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Raikia/SMBCrunch"
github-stars: 170
github-pushed: "2018-03-07"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# smbcrunch

> Three-stage Windows share recon pipeline — find the shares, list them recursively, pull the interesting files.

## What it's for

SMBCrunch automates the most tedious part of share hunting with three Perl scripts that wrap smbclient and hand output to each other: SMBHunt turns a port-445 scan into a share list, SMBList recursively lists every share you can read into a greppable text tree, and SMBGrab fetches files selected from those listings. SMBHunt deliberately tests one credential per server — a single valid share read already returns the server's full share list — and warns before proceeding with possibly-wrong domain credentials to avoid lockouts (override with -f on each script, at your own risk). Listing a share does not equal reading it; SMBList is what proves access.

## Typical use cases

### Discover shares from a port scan

```
./SMBHunt.pl -a 'CORP\user:Password1' -i portscan445.gnmap -o shares_found.txt
```

Outcome: enumerates the shares on every host in the gnmap (or plain host-list) file using the single credential — or a null session when -a is omitted — writing \\server\share lines; --nohidden drops C$/IPC$-style entries.

### Recursively list the accessible shares

```
./SMBList.pl -c credentials_found.txt -s shares_found.txt -o share_listing -m 150
```

Outcome: finds a working credential per share from the credential file, dumps recursive directory listings under share_listing/ with a 150-second cap per share, and produces the greppable index share_listing/ALL_COMBINED_RESULTS.TXT.

### Pull the juicy files from the listings

```
grep -i 'password' share_listing/ALL_COMBINED_RESULTS.TXT | ./SMBGrab.pl -s savedfiles
```

Outcome: downloads every listed file matching the grep into savedfiles/ (annotated with share metadata unless -n is set) instead of mirroring entire shares.

*No corpus evidence — derived from upstream documentation.*
