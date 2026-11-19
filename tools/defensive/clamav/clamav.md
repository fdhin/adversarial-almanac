---
name: "clamav"
categories:
  - protect
version: "1.5.4-1"
homepage: "https://www.clamav.net/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-08-18"
build-system: cmake
meta-checked: "2026-11-09"
github-repo: "Cisco-Talos/clamav"
github-stars: 7516
github-pushed: "2026-11-06"
github-archived: false
github-issues: 452
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 162
gh-activity-checked: "2026-11-09"
---

# clamav

> Open-source antivirus toolkit for Unix: signature-based scanning of files, mail and archives through the one-shot clamscan and the clamd daemon.

## What it's for

ClamAV (Cisco Talos) detects malware using a regularly updated signature database (kept current by freshclam). Its one-shot scanner clamscan builds a fresh engine from the database on every run and scans files, directories, archives and mail containers on demand — no daemon required; clamd with clamdscan provides a resident engine for high-volume or repeated scanning, and clamonacc adds on-access scanning. Detection is signature- and heuristic-based: good for known malware in file stores and mail paths, not a complete endpoint protection suite. Database updates and daemon configuration are separate concerns (freshclam, clamd.conf) from running scans.

## Typical use cases

### Recursive scan of a directory tree

Check a whole directory for malicious files.

```
clamscan -r /var/www
```

Outcome: every file and subdirectory is scanned; clamscan prints per-file verdicts and a final SCAN SUMMARY with known-virus count, engine version, scanned and infected file counts, and elapsed time.

### Report only infected files, with an audit log

Keep a scan trail while suppressing clean-file noise.

```
clamscan -r -i --log=scan.log /home/user
```

Outcome: only infected files are printed to the terminal (-i), while the full report including the summary is written to scan.log.

### Scan a manifest of specific files

Feed clamscan an explicit list of paths.

```
clamscan -f filelist.txt
```

Outcome: each path in filelist.txt (one per line) is scanned instead of a directory argument — useful for re-checking a fixed set of collected artefacts or targets named by another tool.

### Quarantine instead of delete

Move detections out of the way rather than destroying them.

```
clamscan -r --move=$HOME/quarantine /var/tmp
```

Outcome: any file matching a signature is moved into the quarantine directory, which must be writable by the invoking user; --copy keeps originals in place, and --remove deletes infected files outright (the man page warns to be careful with it).

### Debug what libclamav sees in a file

Get verbose engine output while scanning a single artefact.

```
clamscan --debug sample.dmg
```

Outcome: libclamav debug messages are printed as the file is parsed, showing which parser handled the input and the metadata it extracted — the way to understand a surprising verdict or a parser's behavior on an unusual container format.
