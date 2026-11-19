---
name: "pst-utils"
categories:
  - forensics
version: "0.6.76-1.3+b1"
homepage: "https://www.five-ten-sg.com/libpst/"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali]
build-system: autotools
meta-checked: "2026-11-09"
github-repo: "OpenMandrivaAssociation/pst-utils"
github-stars: 0
github-pushed: "2026-09-15"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# pst-utils

> Debian/Kali package for libpst — readpst, lspst, pst2ldif and pst2dii — the classic toolkit for extracting mail and contacts from Outlook PST files.

## What it's for

pst-utils is the Debian and Kali package name for libpst, maintained by the 510 Software Group at five-ten-sg.com (readpst itself grew out of Dave Smith's original libpst reader, with lspst by Joe Nahmias). It reads Outlook PST files — both the 32-bit pre-2003 format and the 64-bit Outlook 2003+ format — and converts them into formats standard tooling can consume: mbox and recursive mbox trees, per-message .eml files, MH, vcard/LDIF contacts (pst2ldif), and DII load files for CT Summation review tools (pst2dii). In a forensic triage its most valuable switch is readpst -D, which includes items marked deleted inside the PST. It extracts the content of a loadable PST; it does not repair a corrupt PST structure.

## Typical use cases

### Convert a PST into a recursive mbox tree

Extract every folder of a mailbox as mbox files.

```
readpst -r -o outdir archive.pst
```

Outcome: creates outdir/ with one directory per PST folder, each holding an `mbox` file (plus `calendar`, `contacts` and `journal` files where those item types exist) — importable into any mbox-capable mail client.

### Dump each message as a separate .eml

Get individual RFC-822 files for a review platform.

```
readpst -e -o outdir archive.pst
```

Outcome: writes every message to its own numbered file with a proper extension (.eml for mail, .ics for appointments, .vcf for contacts), preserving the PST folder structure under outdir/.

### Include deleted items

Recover soft-deleted messages still stored in the PST.

```
readpst -D -e -o outdir archive.pst
```

Outcome: the export additionally contains items flagged as deleted in the PST, which no longer appear in a normal client view — often the most interesting material in an investigation.

### List PST contents without extracting

Triage subjects and contacts before committing to a full export.

```
lspst -l archive.pst
```

Outcome: prints a long-format listing of the PST data (email subjects, contacts) including Date, CC and BCC headers; nothing is written to disk.

*No corpus evidence — derived from upstream documentation.*
