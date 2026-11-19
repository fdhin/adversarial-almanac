---
name: "rarcrack"
categories:
  - passwords
version: "0.2-8"
homepage: "https://github.com/ziman/rarcrack"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2025-04-23"
build-system: other
license: GPL-2.0-only
meta-checked: "2026-11-09"
github-repo: "ziman/rarcrack"
github-stars: 116
github-pushed: "2018-02-17"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rarcrack

> Brute-force password recovery for RAR, ZIP and 7z archives.

## What it's for

Recovers the password of an encrypted RAR, ZIP or 7z archive by brute force when you have the file but not the password. The archive type is auto-detected, up to 12 cracking threads run in parallel, and progress is checkpointed to an XML status file next to the archive so an interrupted run resumes where it stopped. It shells out to unrar/7z/unzip to test candidates, so those backends must be installed. It is a pure brute forcer over a character set — no wordlists or rules; the searched alphabet is changed by editing the `<abc>` element in the XML status file (create it by starting a run for a few seconds first), not via a command-line flag.

## Typical use cases

### Crack an archive with defaults

Start a search on an encrypted archive with default settings.

```
rarcrack secret.rar
```

Outcome: auto-detects the archive type, cracks with two threads, prints current status continuously, and writes a checkpoint file `secret.rar.xml`; when the password is found it is printed and stored in the XML file. Rerunning the same command resumes from the checkpoint instead of starting over.

### Speed up the search with more threads

Use more parallel cracking threads on a multi-core machine.

```
rarcrack encrypted.zip --threads 8
```

Outcome: runs eight cracking threads (12 is the maximum) against `encrypted.zip`, with progress checkpointed to `encrypted.zip.xml` as above.

### Force the archive type

Override auto-detection when the file extension is misleading.

```
rarcrack backup.7z --type 7z
```

Outcome: treats the file as a 7z archive regardless of detection and starts brute forcing it directly.

*No corpus evidence — derived from upstream documentation.*
