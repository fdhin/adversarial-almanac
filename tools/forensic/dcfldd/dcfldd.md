---
name: "dcfldd"
categories:
  - blackarch-forensic
version: "1.9.3"
homepage: "https://github.com/resurrecting-open-source-projects/dcfldd/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-08-09"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "resurrecting-open-source-projects/dcfldd"
github-stars: 129
github-pushed: "2025-06-01"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---
# dcfldd

> The DoD Computer Forensics Lab's enhanced dd — hash while copying, split output with custom naming, verify images, and wipe with a pattern.

## What it's for

dcfldd, originally developed by Nicholas Harbour at the Department of Defense Computer Forensics Lab (DCFL) and now community-maintained, is a modified GNU dd for forensics and security work. On top of plain dd it hashes the input as it is transferred (several algorithms simultaneously, with per-window rolling hashes), writes to multiple files or devices at once, splits output with configurable segment naming, verifies that an image matches its source bit-for-bit, and wipes media with a byte pattern — all with on-screen progress and logs that can be piped to commands. Its default block size is 32 KiB rather than dd's 512 bytes. Like dd it only moves bytes; carving and parsing are other tools' jobs.

## Typical use cases

### Image a disk with simultaneous md5 and sha256

```
dcfldd if=/dev/sdb bs=4096 hash=md5,sha256 md5log=sdb.md5 sha256log=sdb.sha256 of=sdb.img
```

Outcome: a forensic image plus two separate hash files, with both digests calculated in memory during the single read pass — no second pass over the evidence just to hash it; a status line on stderr tracks progress.

### Verify an existing image against the source

```
dcfldd if=/dev/sdb vf=sdb.img
```

Outcome: rereads the source and compares it block-by-block with the image file (`vf=`), reporting whether the copy is a bit-for-bit match; results can be written to a file with `verifylog=` instead of stderr.

### Split the image into named segments

```
dcfldd if=/dev/sdb split=500M splitformat=annnn of=sdb.img
```

Outcome: 500 MiB segments named `sdb.imga0000`, `sdb.imga0001`, ... (the `annnn` format uses letters then digits; the default `nnn` gives `.001`-style numbering). `split=` must appear before the `of=` it applies to.

### Wipe media with a one-byte pattern

```
dcfldd pattern=00 of=/dev/sdb
```

Outcome: fills the target device with the hex byte `00` in place of reading an input (`textpattern=` accepts a text character instead), reporting progress via the status output — a quick, verifiable wipe before disposal or reissue.

*No corpus evidence — derived from upstream documentation.*
