---
name: "rifiuti2"
categories:
  - blackarch-forensic
version: "0.8.2"
homepage: "https://github.com/abelcheung/rifiuti2"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2026-05-04"
build-system: cmake
license: custom:BSD
meta-checked: "2026-11-09"
github-repo: "abelcheung/rifiuti2"
github-stars: 155
github-pushed: "2025-11-11"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# rifiuti2

> Windows Recycle Bin forensics: parse legacy INFO2 and modern $Recycle.Bin deletion records.

## What it's for

Rifiuti2 is a modern rewrite (by Abel Cheung) of Foundstone's classic `rifiuti`
for Windows Recycle Bin forensics — see the original [[rifiuti]] note for the
ancestor tool. It ships two binaries: `rifiuti` parses the single `INFO2` index
used by Windows 95 through 2003, and `rifiuti-vista` parses the per-user `$I`
index files inside `$Recycle.Bin` on Vista and later. For each deleted item it
recovers the original path, deletion time, size, and whether the item was later
purged from the bin. It reports metadata only — it does not recover the deleted
content itself.

## Typical use cases

### Parse a legacy INFO2 index

Read deletion records from a Windows 95–2003 Recycle Bin file.

```
rifiuti INFO2
```

Outcome: prints one tab-separated line per deleted file (deletion time,
original path, size) to stdout, or an error if the file is not an INFO2 index.

### Parse a Vista+ $Recycle.Bin tree as XML

Process every user SID's `$I` files under an extracted Recycle Bin directory.

```
rifiuti-vista -f xml -z -o result.xml /mnt/case/$Recycle.Bin
```

Outcome: walks the directory for index files and writes `result.xml` with each
item's original path, deletion time (converted to local time by `-z`) and size.

### Export machine-readable JSON

Produce structured output for scripted post-processing.

```
rifiuti-vista -f json -o result.json /mnt/case/$Recycle.Bin
```

Outcome: writes a JSON document describing every deleted item, suitable for
ingestion by other tooling.

### Decode a non-Unicode legacy INFO2

Handle pre-Unicode INFO2 records whose filenames use a Windows codepage.

```
rifiuti -l CP932 -t ';' -n INFO2
```

Outcome: decodes filenames with the Japanese CP932 codepage and prints
semicolon-separated fields with no header row.

### Scan the Recycle Bin of a running Windows system

Analyze the local machine in place instead of an extracted copy.

```
rifiuti-vista --live -z
```

Outcome: scans the live system's Recycle Bin and prints each deletion record
with times adjusted to local time.

*No corpus evidence — derived from upstream documentation.*
