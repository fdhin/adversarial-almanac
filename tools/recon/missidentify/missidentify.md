---
name: "missidentify"
categories:
  - blackarch-recon
version: "1.0"
homepage: "https://missidentify.sourceforge.net/"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2026-09-14"
build-system: autotools
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "Seabreg/missidentify"
github-stars: 3
github-pushed: "2019-05-19"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# missidentify

> Finds Windows PE executables hiding behind innocent file extensions.

## What it's for

Miss Identify (by Jesse Kornblum) reads the header of every file it is given and reports those that are PE executables — programs, DLLs, drivers — regardless of what their name claims. Its default mode is the forensic niche: list only executables whose extension is NOT one of `.exe .com .sys .dll`, i.e. malware or policy evaders disguised as text, images or archives. It examines local files only (no network), can be fooled by files with an unusually large (>1024 byte) gap between the MZ and PE headers, and works nicely in pipes over `find`/`xargs` output. No corpus evidence — derived from upstream documentation.

## Typical use cases

### Hunt disguised executables

Find PE files masquerading as something else.

```
missidentify -r /mnt/samples
```

Outcome: recursively (`-r`) walks the directory and prints the full path of every PE executable whose extension is not one of the known executable extensions — each hit is a file that runs as a program while looking like data.

### Inventory all executables

List every PE file, whatever its name.

```
missidentify -rab /mnt/samples | sort | tee pe-inventory.txt
```

Outcome: `-a` widens the report to all executables and `-b` strips directory prefixes, yielding a sorted, bare-filename inventory of PE files written to `pe-inventory.txt` — useful as a baseline snapshot for later diffing.

### Screen an upload directory in a pipeline

Check arbitrary file lists without recursion.

```
find /var/www/uploads -type f -print0 | xargs -0 missidentify
```

Outcome: each file found is passed as an argument and header-checked; any PE content in the upload tree is printed to stdout while non-executables pass silently, so a single hit means someone uploaded a runnable Windows binary.

### Show strings alongside each hit

Extract a quick fingerprint with every detection.

```
missidentify -r -S 8 /mnt/samples
```

Outcome: for every detected executable, prints strings of length 8 or greater found in the file before the filename (`-s` puts the filename first instead); a fast way to spot embedded URLs, PDB paths or packer signatures during triage.
