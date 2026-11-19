---
name: "sambascan"
categories:
  - blackarch-scanner
version: "0.5.0"
homepage: "http://sourceforge.net/projects/sambascan2/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "Seabreg/sambascan"
github-stars: 0
github-pushed: "2019-03-22"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sambascan

> Bash script that sweeps a host or network for SMB/Samba shares and indexes the contents of every public share it finds.

## What it's for

sambascan (upstream name `sambascan2`) automates SMB share discovery: given a network or host list it resolves each host, enumerates its Windows shares, and walks the contents of the public ones, storing what it finds so it can be searched afterwards. It drives `nmap`, `nmblookup` and `smbclient` under the hood. Since version 0.4.0 it can store per-host login credentials to reach password-protected shares — the README warns those are saved in plaintext and can leak via the process list. It is share enumeration and file indexing only: no authentication attacks, and it does not fully traverse private shares.

## Typical use cases

### Enumerate shares across a network

Find every SMB host and public share on a subnet.

```
sambascan -V 2 198.51.100.0/24
```

Outcome: resolves each responding host, lists its shares, and indexes the files inside public shares in its working directory; `-V 2` raises verbosity so scanning progress is reported.

### Scan hosts from a file

Feed a prepared target list instead of a network spec.

```
sambascan -f targets.txt
```

Outcome: reads targets from the file and runs the same share enumeration over them; `-o` instead re-scans all previously indexed hosts.

### Search indexed shares for file names

Turn the collected index into a queryable file database.

```
sambascan -s password -s backup -m 1024 198.51.100.10
```

Outcome: after scanning, prints matching files found in the indexed shares; `-s` may be given multiple times and `-m`/`-M` restrict matches to a minimum/maximum file size in bytes.

### Store credentials for protected shares

Let the scanner authenticate against password-protected hosts.

```
sambascan -a 'LOCALHOST:user%Password1::'
```

Outcome: adds the login to sambascan's database in `hostname:user%password:[ip]:[share]` form (`*:user%password::` is the catch-all entry); `-g`, `-d` and `-l` retrieve, delete and list stored logins. The password is kept in plaintext.

*No corpus evidence — derived from upstream source (Seabreg/sambascan mirror of sambascan2 0.5.0; usage read from `sambascan2.in`, packaged by BlackArch as `sambascan`).*
