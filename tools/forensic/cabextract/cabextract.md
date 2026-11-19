---
name: "cabextract"
categories:
  - forensics
version: "1.11-3"
homepage: "https://www.cabextract.org.uk/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-06-22"
build-system: autotools
meta-checked: "2026-11-09"
github-repo: "pld-linux/cabextract"
github-stars: 5
github-pushed: "2026-05-20"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cabextract

> Free Software extractor for Microsoft cabinet (.CAB) archives, including embedded and self-extracting cabinets.

## What it's for

cabextract (by Stuart Caie, on the portable libmspack library) unpacks
Microsoft cabinet files — the `.CAB` format used by Windows Update,
installers, and Windows CE setup packages — supporting every cabinet format
variant and feature. Beyond plain `.cab` files it handles multi-part
cabinet sets (you name only the first file), self-extracting executable
cabinets, and it will look inside other files for embedded cabinets, which
makes it a routine step when triaging Windows software or malware
droppers. It verifies and, if asked, salvages corrupted archives, but it is
an extractor only — it does not create cabinets (see `lcab` for that) and
does not analyze the payload beyond unpacking it.

## Typical use cases

### Extract a cabinet

Unpack all files from a cabinet into the current directory.

```
cabextract update.cab
```

Outcome: extracts every file, preserving internal directory structure,
permissions, and dates; for a multi-part set, naming the first cabinet
extracts the whole set.

### List cabinet contents without extracting

Preview what is inside before writing anything.

```
cabextract -l update.cab
```

Outcome: prints a table of file sizes, timestamps, and names — no files are
written.

### Test integrity

Validate the archive and checksum unpacked data without saving it.

```
cabextract -t update.cab
```

Outcome: unpacks in memory and reports per-file MD5 checksums plus a final
"no errors" (or error) verdict — a quick triage signal for tampered or
corrupt packages.

### Extract only matching files into a target directory

Pull a subset of the payload into a sandbox directory.

```
cabextract -d out -F '*.exe' update.cab
```

Outcome: writes only files matching the pattern (repeatable `-F`) into
`out/`, leaving the rest of the archive untouched.

### Salvage a damaged cabinet

Recover whatever is readable from a corrupt or truncated archive.

```
cabextract -f broken.cab
```

Outcome: extracts as much as possible instead of aborting on the first
error, skipping blocks it cannot decompress.

### Unpack an embedded cabinet from a dropper

Extract cabinets hidden inside an executable or document.

```
cabextract -d out installer.exe
```

Outcome: scans the executable for embedded cabinet data and unpacks any
cabinets it finds into `out/` — add `-p` to pipe extracted files to stdout
instead.

*No corpus evidence — derived from upstream documentation.*
