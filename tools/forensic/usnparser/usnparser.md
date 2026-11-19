---
name: "usnparser"
categories:
  - blackarch-forensic
version: "4.1.5"
homepage: "https://pypi.org/project/usnparser/#files"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: python
license: APACHE
meta-checked: "2026-11-09"
github-repo: "PoorBillionaire/USN-Journal-Parser"
github-stars: 118
github-pushed: "2022-07-15"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# usnparser

> Python CLI that parses an NTFS USN journal file into text, CSV, body or TLN output.

## What it's for

usnparser (Adam Witt, PyPI package `usnparser`) parses an already-extracted NTFS USN change journal — the `$Extend\$UsnJrnl:$J` stream that logs per-volume file metadata changes — and prints each record's timestamp, file name, attributes and reason flags. Output formats cover plain text, CSV, mactime body, and TLN timeline format, plus full JSON record dumps. It is pure Python, so it runs anywhere against a journal dump rather than needing Windows. It does not extract the `$J` stream itself — pull that off the volume first (e.g. with ExtractUsnJrnl, see [[extractusnjrnl]]). For a Windows GUI/converter over the same artifact, see [[usnjrnl2csv]]. Upstream script is `usn.py`; BlackArch installs it as `usn`.

## Typical use cases

### Parse a journal dump to text

Baseline conversion of an extracted journal.

```
usn -f usnjournal.bin -o usn-out.txt
```

Outcome: writes every USN record to usn-out.txt in the default `timestamp | file | attributes | reasons` format. Both `-f` (input journal) and `-o` (output file) are required.

### Export CSV for spreadsheets

Tabular output for review or pivoting.

```
usn -f usnjournal.bin -o usn-out.csv -c
```

Outcome: emits the same records with a `timestamp,filename,fileattr,reason` header, one record per line — open it directly in a spreadsheet or pandas.

### Emit timeline formats

Feed a merged forensic timeline.

```
usn -f usnjournal.bin -o usn-tln.txt -t -s workstation-01
```

Outcome: outputs TLN lines `time|USN|system|user|file:reasons` tagged with the system name given by `-s`; swap `-t` for `-b` to get mactime bodyfile format instead.

### Dump every record property

Full-fidelity output for deep dives.

```
usn -f usnjournal.bin -o usn-full.txt -v
```

Outcome: writes each record's complete USN properties as JSON, for when the summarized reason flags are not enough.

*No corpus evidence — derived from upstream documentation.*
