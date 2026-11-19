---
name: "analyzemft"
categories:
  - blackarch-forensic
version: "v3.1.1.0.r0.gd82bc98"
homepage: "https://github.com/dkovar/analyzeMFT"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-03"
build-system: python
license: CPL
meta-checked: "2026-11-09"
github-repo: "dkovar/analyzeMFT"
github-stars: 537
github-pushed: "2026-07-06"
github-archived: false
github-issues: 19
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# analyzemft

> Decode NTFS Master File Table records into CSV, body files, and timelines.

## What it's for

AnalyzeMFT is a Python parser for the NTFS Master File Table ($MFT). It turns
raw binary MFT records — file names, Standard Information and FILE_NAME
timestamp variants, sizes, flags, parent links — into structured output for
forensic review: CSV, JSON, XML, Excel, SQLite, or body/timeline formats
that feed directly into `mactime` and other timeline tools. It handles both
allocated and deleted entries and tolerates corrupted records, which makes
it a standard first stop when reconstructing file system activity from an
NTFS image. It parses one artifact — the MFT — and does not carve file
contents or parse other registry/filesystem artifacts.

## Typical use cases

### Parse an MFT to CSV

Convert a captured MFT into a spreadsheet-ready record listing.

```
analyzeMFT.py -f mft.img -o mft.csv
```

Outcome: writes one row per MFT record with filenames, attributes, and
timestamp variants; the CSV is the base for filtering by record number,
parent directory, or flag.

### Produce a body file for timeline analysis

Emit a Sleuth Kit body file using Standard Information timestamps.

```
analyzeMFT.py -f mft.img -b body.txt --bodystd
```

Outcome: writes a `mactime`-compatible body file (`mactime -b body.txt -d`)
covering every MFT record, including deleted entries.

### Emit a CSV timeline in local time

Get a ready-made timeline with timestamps rendered in the analyst's local
timezone.

```
analyzeMFT.py -f mft.img -c timeline.csv -l
```

Outcome: writes a CSV-format timeline file of all records without a separate
body-file/mactime pass.

### Flag timestamp anomalies

Surface records where FILE_NAME and Standard Information timestamps
disagree — a classic anti-forensics signal.

```
analyzeMFT.py -f mft.img -o mft.csv -a
```

Outcome: the CSV marks records with timestamp inconsistencies for triage.

### Export structured data with hashes

Dump JSON with hashes computed over data-run-backed attributes.

```
analyzeMFT.py -f mft.img -o mft.json --json --hash
```

Outcome: writes a JSON export including MD5/SHA256/SHA512/CRC32 hashes of
resident and non-resident attribute data, hashing in parallel by default.

*No corpus evidence — derived from upstream documentation.*
