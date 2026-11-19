---
name: "7zip"
categories:
  - forensics
version: "26.05-1"
homepage: "https://www.7-zip.org"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali]
arch-builddate: "2026-10-22"
build-system: make
license: LGPL-2.1-or-later AND LicenseRef-UnRAR AND BSD-3-Clause AND BSD-2-Clause
meta-checked: "2026-11-09"
github-repo: "ip7z/7zip"
github-stars: 4422
github-pushed: "2026-11-07"
github-archived: false
github-issues: 212
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 10
gh-activity-checked: "2026-11-09"
---

# 7zip

> Archiver with a high compression ratio — the standard command-line tool for extracting, creating and inspecting archives encountered during an engagement.

## What it's for

Compressing and decompressing files. The command-line binaries are `7z` (full) and `7za` (standalone); both create and read 7z and zip and unpack many other formats. In security work it is the default extractor for archives recovered from targets — backup bundles, exfil files, password-protected attachments — and a quick way to package data with a password. It only reads and writes archives; to attack archive passwords, convert them for a cracker instead (e.g. john's `7z2john`).

## Typical use cases

### Extract an archive keeping its directory structure

```
7z x "Access Control.zip"
```

Outcome: every file lands under the current directory with the paths stored inside the archive. If the archive is encrypted, the command prompts for the password (or takes it inline as `-pSecret123`, no space after `-p`).

### Extract just the files, flattened

```
7z e s.blade.7z
```

Outcome: contents are dumped straight into the current directory, ignoring the archive's internal folder structure — convenient when one interesting file sits at a known depth.

### Package a directory, preserving symlinks

```
7za a /backup/project.zip -tzip -snl -mmt -- *
```

Outcome: a zip of all files including dotfiles (`-- *`), with symlinks stored as links (`-snl`) rather than followed, and multithreaded compression (`-mmt`). Use `-t7z` instead of `-tzip` for higher compression, and add `-p` to encrypt the contents.

### Inspect an archive's metadata without extracting

```
7z l -slt uploaded-file.zip
```

Outcome: a technical listing of every entry — path, timestamps, compressed and uncompressed sizes, CRC, attributes. This is the usual first triage step on an unknown archive: it exposes hidden paths and abnormal expansion ratios before anything is written to disk.
