---
name: "indxparse"
categories:
  - blackarch-forensic
version: "198.a977192"
homepage: "http://www.williballenthin.com/forensics/mft/indxparse/"
packaged-by: [blackarch]
blackarch-builddate: "2023-07-23"
build-system: scripts
license: APACHE
meta-checked: "2026-11-09"
github-repo: "williballenthin/INDXParse"
github-stars: 229
github-pushed: "2023-11-01"
github-archived: false
github-issues: 14
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# indxparse

> Python suite by Willi Ballenthin for parsing NTFS INDX ($I30) records into CSV or bodyfile timelines, including deleted entries found in slack space.

## What it's for

Every NTFS directory carries an INDX ($I30) attribute whose records hold filename, sizes and timestamps for each file in the directory; the attribute's slack space can still contain entries for deleted files. INDXParse turns extracted INDX data into analyst-ready output — CSV by default, or a bodyfile row set for timeline tools — and flags slack entries with their offsets. It operates on artifacts extracted from an image (e.g. an `$I30` file pulled out with an imaging tool); it does not work against a live system. The package also ships MFT-side utilities (`list_mft.py`, `tree_mft.py`, `extract_mft_record_slack.py`, `MFTView.py`, `fuse-mft.py`) and `INDXFind.py`, a carver for cluster-aligned INDX records in raw images — for carving INDX records out of memory dumps and unallocated chunks see also [[indxcarver]].

## Typical use cases

### Parse an extracted $I30 file to CSV

Turn one extracted INDX file into spreadsheet-ready rows.

```
INDXParse.py '$I30' > indx.csv
```

Outcome: one CSV row per file entry with filename, physical/logical size and modified/accessed/changed/created timestamps on stdout.

### Emit bodyfile rows for a timeline

Produce mactime-compatible output instead of CSV.

```
INDXParse.py -b '$I30' >> timeline.body
```

Outcome: bodyfile (v3) rows appended to `timeline.body`, ready for timeline analysis tools.

### Surface deleted entries in INDX slack space

Recover directory entries that were deleted but remain in the attribute's slack.

```
INDXParse.py -d '$I30'
```

Outcome: CSV output as usual, with extra rows for slack entries tagged `(slack at ###)` where `###` is the hex offset of the entry inside the INDX file.

### Carve INDX records from a raw image, then parse them

Harvest cluster-aligned INDX records straight from a raw disk image (e.g. an ewfmount'd E01).

```
python INDXFind.py /mnt/ewf/ewf1
INDXParse.py INDX_records.raw
```

Outcome: `INDXFind.py` scans 4096-byte-aligned INDX signatures and writes every matching record to `INDX_records.raw` in the current directory; the second command parses those records into the usual CSV output.

*No corpus evidence — derived from upstream documentation.*
