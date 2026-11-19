---
name: "python-peid"
categories:
  - blackarch-binary
version: "2.2.1"
homepage: "https://github.com/packing-box/peid"
packaged-by: [blackarch]
blackarch-builddate: "2026-11-01"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "packing-box/peid"
github-stars: 153
github-pushed: "2026-10-25"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 13
gh-activity-checked: "2026-11-09"
---

# python-peid

> Python implementation of PEiD: detects packers on Windows PE files by signature matching against a merged database of 5,500+ packer signatures.

## What it's for

Use this to identify the packer, protector, or compiler of a Windows executable during malware triage, so you know which unpacking step comes next. It is a modern reimplementation of the classic PEiD: the `peid` command checks an input executable against a signature database combining entries from app-peid, pev, ExeinfoASL, MalScan, and PEiD Tab, matching by default on the entry point; `peid-db` inspects and filters the signature database itself, and `peid-sig` creates new signatures from packed samples you already know, so the database grows with your corpus. It identifies only — it does not unpack. The PyPI package is `peid`.

## Typical use cases

### Identify the packer of an executable

Run signature detection against a PE file.

```
peid program.exe
```

Outcome: prints the signature matches for `program.exe` (packer name and, optionally, version/author), telling you which packer to expect before static analysis.

### Match against a custom signature database

Use your own or third-party signatures instead of the embedded set.

```
peid program.exe --db custom_sigs_db.txt
```

Outcome: detection runs against `custom_sigs_db.txt`, so updated or hand-grown signature files can replace the embedded database.

### Inspect signatures in a database

Search the signature database for a given packer.

```
peid-db --db path/to/userdb.txt --filter UPX
```

Outcome: lists every signature matching `UPX` from the given database file, useful for checking coverage before relying on it.

### Create a signature from known samples

Teach the tool a new packer from samples you have already identified.

```
peid-sig sample1.exe sample2.exe --packer "MyPacker 1.0" --db path/to/userdb.txt
```

Outcome: derives a common-byte signature from the given packed executables and appends it to the target database as `MyPacker 1.0`, which `peid --db` can then match.

*No corpus evidence — derived from upstream documentation.*
