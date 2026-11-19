---
name: "slipit"
categories:
  - blackarch-misc
version: "1.0.1"
homepage: "https://github.com/usdAG/slipit"
packaged-by: [blackarch]
blackarch-builddate: "2026-08-10"
build-system: python
license: GPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "usdAG/slipit"
github-stars: 98
github-pushed: "2023-02-09"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# slipit

> Builds and edits ZipSlip test archives: appends entries whose traversal-prefixed names escape the extraction directory.

## What it's for

slipit (usd AG) creates or modifies zip, tar, tgz and bz2 archives containing path-traversal payloads — entry names like `..\..\..\..\..\..\file` that climb out of the extraction directory when an application unpacks them unsafely (the ZipSlip class of vulnerabilities). Invoke it with an archive and any number of files to append those files with a traversal prefix (default depth 6, Windows `\` separator); invoke it with the archive alone to list its contents. It supports Unix separators and custom traversal sequences, incremental depth sweeps, dummy content instead of real files, tar symlinks, and can strip traversal entries back out of an archive. It is a payload generator for testing extraction code — it does not perform the extraction itself.

## Typical use cases

### Append a traversal-prefixed file to an archive

Turn an ordinary file into a ZipSlip probe inside a zip.

```
slipit example.zip payload.txt
```

Outcome: `example.zip` gains the entry `..\..\..\..\..\..\payload.txt` (depth 6, Windows separators) carrying payload.txt's contents; the entry is visible when listing the archive.

### Sweep multiple traversal depths at once

Cover several escape depths when you do not know the extraction directory's distance from the filesystem root.

```
slipit archive.tar.gz payload.txt --depth 6 --increment 1
```

Outcome: the archive receives payload entries at every depth from the increment start to the maximum (e.g. `..\`, `..\..\`, ... `..\..\..\..\..\..\`), one per depth.

### Pack dummy content without real files

Test an extractor's filename handling without preparing actual payload files.

```
slipit example.zip marker.txt --static 'test-marker'
```

Outcome: an entry named with the traversal prefix and `marker.txt` is added whose contents are the literal string, ignoring any file of that name on disk.

### Plant a symlink in a tar archive

Escape via a symlink for extractors that preserve them.

```
slipit --symlink /etc/passwd example.tar note.txt
```

Outcome: `example.tar` gains a traversal-prefixed entry stored as a symlink to the given target (tar-family formats only).

### Verify or sanitize an archive

Inspect an archive's entries, or strip traversal payloads from one.

```
slipit example.zip
slipit --clear example.zip
```

Outcome: the first command lists every entry with timestamp and size (traversal paths plainly visible); `--clear` removes all traversal-prefixed entries, leaving legitimate files intact.

*No corpus evidence — derived from upstream documentation.*
