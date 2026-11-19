---
name: "recomposer"
categories:
  - blackarch-automation
version: "2.90f85ed"
homepage: "https://github.com/secretsquirrel/recomposer"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "secretsquirrel/recomposer"
github-stars: 130
github-pushed: "2013-10-30"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# recomposer

> Randomly rewrites Win32/64 PE files — renaming sections, flipping flags and padding code caves with nops — so each copy has a fresh hash but still executes.

## What it's for

Changing a PE binary's bytes so that hash-based lookups no longer match the original file. The motivating use case is uploading a binary to multi-scanner and sandbox services without making the original sample's hash searchable through that upload: recomposer alters section names, section flags, the file name, and injects nops into code caves larger than 20 bytes, so the recomposed file hashes differently while remaining executable. Upstream measured 11200 variants from one binary with no hash collisions and ssdeep similarity of 77–94%. It is not an AV-evasion packer: it does not change what the program does or how it looks to behavioral detection, and the `.text` section hash can survive unchanged (it warns about this) — the author suggests upx-encoding the output when that matters.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Auto-recompose a binary for a fresh hash

```
recomposer -f ./tool.exe -a
```

Outcome: prints the parsed section table, randomly renames sections and flips some flags, then writes a randomly-named copy (for example `zYmycO4NO2LYW.exe`) into the `updatedfile/` directory. The run is appended to `log_recomposer.txt` in the format `filename|hash|changedfile|changedfile_hash`, so you can always map each variant back to its source hash. If you see `[!] Warning, .text section hash is not changed!`, that section of the output is still hash-matchable to the original.

### Hand-edit sections like a mini PE editor

```
recomposer -f ./tool.exe -m
```

Outcome: instead of randomizing, drops you into an interactive menu: pick a section number, then choose to change its name or toggle its flags (`IMAGE_SCN_MEM_READ`, `IMAGE_SCN_CNT_CODE`, ...; commands `zero`, `help`, `write`, `exit`), with `write` committing the change. The result lands in `updatedfile/` as `change.tool.exe` (or the name set via `-o`), and the change is logged with before/after hashes.
