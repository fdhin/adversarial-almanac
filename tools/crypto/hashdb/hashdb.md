---
name: "hashdb"
categories:
  - blackarch-crypto
version: "1089.1da1b9f"
homepage: "https://github.com/NPS-DEEP/hashdb/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2023-02-28"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "NPS-DEEP/hashdb"
github-stars: 45
github-pushed: "2019-03-21"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hashdb

> Build, merge, and query databases of fixed-size block hashes to find known or blacklisted data inside raw media images.

## What it's for

hashdb (from the Naval Postgraduate School's digital-forensics group) is a block-hash toolkit: instead of whole-file digests it stores MD5 hashes of fixed-size blocks (default 512 bytes) aligned along block boundaries, taken from every block (and recursively from embedded data such as archives). Because matching happens at block granularity, it can identify known material in media images even when files are fragmented, carved, or partially damaged — the classic known-file filter used in forensic triage (its block scanner is also used by bulk_extractor). The workflow is: create a database, ingest reference corpora (a directory tree, NIST NSRL tab files, or JSON), then scan media images, hash lists, or single block hashes for matches, refining databases with set operations (add, intersect, subtract). It is not a password-hash tool — it never cracks anything, it only reports where known block data occurs.

## Typical use cases

### Create a database and ingest a reference corpus

```sh
hashdb create evidence.hdb
hashdb ingest -r reference-set evidence.hdb /opt/reference-files
```

Outcome: creates a new block-hash database (512-byte default block size, changeable with `-b`), then recursively block-hashes every file under `/opt/reference-files`, recording `reference-set` as the repository name attached to each imported hash.

### Import an NSRL tab file

```sh
hashdb import_tab evidence.hdb nsrl_file.txt
```

Outcome: loads block hashes from an NSRL/RDS-style tab-delimited file into the database, giving scans a NIST known-file baseline to filter against.

### Scan a media image for known blocks

```sh
hashdb scan_media evidence.hdb disk.dd
```

Outcome: walks the raw image hashing every block and prints one JSON record per matching block hash (with `-j e` for fully expanded output), naming the repository and file the block is known from — showing an examiner which known material is present and where.

### Look up a single block hash

```sh
hashdb scan_hash evidence.hdb 4628b9b168ef075609dbc91b6284e286
```

Outcome: prints the JSON record for that hex block hash (which repository and source file it belongs to) if the database contains it, nothing otherwise.

### Filter one database against another

```sh
hashdb subtract suspects.hdb known-good.hdb interesting.hdb
```

Outcome: builds `interesting.hdb` containing only block hashes present in `suspects.hdb` but absent from `known-good.hdb` — removing benign/OS blocks so the remainder is worth reviewing. `add` and `intersect` perform the analogous union and intersection.

### Inspect database contents

```sh
hashdb size evidence.hdb
hashdb sources evidence.hdb
```

Outcome: prints the sizes of the database's internal tables, then the repository-name and filename source listing describing where each set of hashes came from.

*No corpus evidence — derived from upstream documentation.*
