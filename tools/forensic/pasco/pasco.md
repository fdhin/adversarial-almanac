---
name: "pasco"
categories:
  - blackarch-forensic
version: "20040505_1"
homepage: "https://www.jonesdykstra.com/"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2025-12-21"
build-system: scripts
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
worth: 1.1
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# pasco

> Parse Internet Explorer's history index.dat into delimited rows — URLs visited, cache file paths, timestamps and HTTP headers.

## What it's for

Pasco (Keith J. Jones, Foundstone, 2003) examines Internet Explorer's cache/history `index.dat` files for forensic purposes. Given one file it prints one delimited row per activity record: record type (`URL`, `REDR`, `LEAK`), the URL — including `file://` and `Visited:` style entries — modified and access times, the local cached filename and its directory, and any HTTP headers stored with the record; a header line names the file and its format version. That reconstructs browsing and local-file-access activity from a seized profile. Output is tab-delimited by default, `-t` changes the delimiter for CSV import, and `-d` additionally parses activity records found in the file's deleted-record space. It handles the classic pre-Vista formats (index.dat v4 and v5.x, NT4 through the IE6-era XP); modern Windows keeps this data in ESE databases (WebCache), which needs different tooling. Its Foundstone sibling for the cookies index.dat is [[galleta]].

## Typical use cases

### Dump browsing history

First pass over a profile's index.dat.

```
pasco index.dat
```

Outcome: a header line (`TYPE URL MODIFIED TIME ACCESS TIME FILENAME DIRECTORY HTTP HEADERS`) followed by one tab-delimited row per activity record on stdout — the visited-URL and cache-activity picture of the user account.

### Export as CSV

Change the delimiter for spreadsheets and timelines.

```
pasco -t , index.dat > history.csv
```

Outcome: the same records comma-delimited into `history.csv`, ready to open in a spreadsheet or merge into a user-activity timeline; `-t` accepts any delimiter string.

### Include deleted records

Recover entries the browser marked as removed.

```
pasco -d -t , index.dat > history-with-deleted.csv
```

Outcome: activity records parsed out of the file's deleted-record space are added to the output alongside the live ones — often the only surviving trace after a history wipe.

*No corpus evidence — derived from upstream documentation.*
