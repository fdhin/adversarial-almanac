---
name: "rifiuti"
categories:
  - forensics
version: "20040505-4"
homepage: "https://sf.net/projects/odessa"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali, nixpkgs]
build-system: other
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# rifiuti

> Keith Jones' Foundstone-era parser that reads Windows Recycle Bin INFO2 records into a forensic listing.

## What it's for

rifiuti (by Keith J. Jones, part of the Foundstone Odessa project alongside [[pasco]] and [[galleta]]) parses the `INFO2` file of a classic (Windows 95 through XP) Recycle Bin and extracts, per deleted file: the deletion time, the original drive number, the original full path, and the size. Output is tab-separated to stdout, ready for spreadsheets or further scripting. It works on a single INFO2 file copied off an image — no live Windows needed. It does not handle the post-Vista Recycle Bin format (`$I...`/`$R...` file pairs); for those use [[rifiuti2]], the independently maintained rewrite by Abel Cheung.

## Typical use cases

### Dump the Recycle Bin index

Parse an INFO2 file pulled from an evidence image.

```
rifiuti INFO2
```

Outcome: one line per deleted file on stdout with deletion time, drive number, original path, and size — a quick view of what the user deleted and when.

### Produce CSV for a report

Change the field delimiter so the listing drops straight into a spreadsheet.

```
rifiuti -t , INFO2 > recyclebin.csv
```

Outcome: the same records with comma as the field separator (default is TAB), written to `recyclebin.csv` for import or pivoting in a report.

*No corpus evidence — derived from upstream documentation.*
