---
name: "nasnum"
categories:
  - blackarch-recon
version: "5.df5df19"
homepage: "https://github.com/tcstool/nasnum.git"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "tcstool/nasnum"
github-stars: 6
github-pushed: "2013-06-08"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nasnum

> One-shot NAS enumerator across SMB shares, NFS exports, SNMP and anonymous FTP.

## What it's for

Nasnum (Russell Butturini, 2013) is a bash wrapper that aims every common storage protocol at a single network-attached storage device and collects what sticks: it enumerates SMB shares (smbclient), NFS exports (showmount), SNMP community strings and connection data (snmpwalk — tested against EMC and Buffalo devices), and anonymous-FTP directory listings (ncftp). Give it an IP, an HTML report path and protocol flags, and it produces a written report of everything it could read without credentials. Two caveats: it wipes `*.txt` in its working directory at startup, so run it from a scratch directory, and by default it mounts the shares it finds (`-q` suppresses that). It is a 2013-era script against hopefully-patched devices — treat it as a quick misconfiguration audit, not a modern NAS assessment. No corpus evidence — derived from upstream documentation.

## Typical use cases

### Full protocol audit of one storage device

Try everything against a NAS and get the report.

```
nasnum 198.51.100.10 nas-report.html -s -n -b -f
```

Outcome: probes SMB (`-s`), NFS (`-n`), SNMP (`-b`, guessing common community strings) and anonymous FTP (`-f`) in one run, mounts enumerable shares, and writes the consolidated HTML report to `nas-report.html`.

### NFS-only check without mounting

Enumerate exports quietly.

```
nasnum 198.51.100.10 nfs-report.html -n -q
```

Outcome: runs only the NFS enumeration via showmount, and `-q` skips mounting the discovered exports — a read-only inventory of what the device exports, saved to the HTML report.

### Clean up after a share-hunting run

Audit SMB and remove the droppings.

```
nasnum 198.51.100.10 report.html -s -c
```

Outcome: enumerates and samples SMB shares as usual, and `-c` unmounts and removes the temporary files the script created when it finishes — pair it with running from an empty directory anyway, since startup still clears `*.txt` in the current directory.
