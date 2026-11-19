---
name: "dc3dd"
categories:
  - blackarch-forensic
version: "7.2.646"
homepage: "http://sourceforge.net/projects/dc3dd"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2017-04-10"
build-system: perl
license: GPL3
meta-checked: "2026-11-09"
github-repo: "aur-archive/dc3dd"
github-stars: 0
github-pushed: "2015-08-15"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---
# dc3dd

> The U.S. Department of Defense Cyber Crime Center's patched dd — forensic imaging with on-the-fly hashing, split output, verification and wiping in a single pass.

## What it's for

dc3dd (from the DoD Cyber Crime Center, DC3) patches GNU dd with the features forensic acquisition needs: compute one or more hashes (md5, sha1, sha256, sha512) of the source while imaging, write to multiple outputs or size-bounded segments at once, send hashes and a full session log to files, verify a restored image, and wipe devices with optional verification. Options follow dd's `if=/of=` syntax plus dc3dd extensions (`hash=`, `ofs=`, `hof=`, `wipe=`), so it drops into existing dd workflows. It is an imaging/acquisition tool — it does not parse filesystems or carve files.

## Typical use cases

### Image a device with two hashes and a session log

```
dc3dd if=/dev/sda of=suspect.img hash=md5 hash=sha1 log=suspect.txt
```

Outcome: a bit-for-bit image of the device plus md5 and sha1 digests of the source, both computed during the single read pass; the log file records the hashes, bytes copied and timing for the case documentation.

### Image directly into size-bounded segments

```
dc3dd if=/dev/sda ofs=suspect.img.000 ofsz=650M hash=sha256 log=suspect.txt
```

Outcome: writes CD-sized (650 MiB) segments `suspect.img.000`, `.001`, ... while hashing the whole source in the same pass — replacing a `dd | split` pipeline whose intermediate pipe defeats hashing.

### Restore a segmented image to a device and verify it

```
dc3dd ifs=suspect.img.000 hof=/dev/sdb hash=sha256 log=restore.txt
```

Outcome: reads the segmented image set back (`ifs=`), writes it to the target device, and hashes only the bytes actually written (`hof=`) so the digest is directly comparable with the original acquisition hash; the restore log shows the verification outcome.

### Wipe a device with verification

```
dc3dd hwipe=/dev/sdb hash=sha256 log=wipe.txt
```

Outcome: overwrites the device and then hashes the result to certify the wipe (`hwipe=`); the plain `wipe=` form performs the same overwrite without the post-wipe hash check.

*No corpus evidence — derived from upstream documentation.*
