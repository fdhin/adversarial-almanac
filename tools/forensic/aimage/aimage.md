---
name: "aimage"
categories:
  - blackarch-forensic
version: "3.3.0"
homepage: "https://github.com/Obsidian-Covenant/aimage"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-03"
build-system: autotools
license: custom
meta-checked: "2026-11-09"
github-repo: "Obsidian-Covenant/aimage"
github-stars: 0
github-pushed: "2025-12-14"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# aimage

> The AFF imager: acquires a device (or a network stream) directly into a compressed, hash-bearing Advanced Forensic Format file.

*No corpus evidence — derived from upstream documentation.*

## What it's for

aimage creates AFF disk images at acquisition time: it reads a block device (or standard input, or a TCP stream) and writes an [[afflib]]-format file that stores the data compressed (zlib or LZMA) alongside automatically computed MD5/SHA1/SHA256 hashes, plus optional dmesg/ifconfig snapshots and arbitrary case-metadata segments. Because metadata and data live in one file, the output is self-documenting for chain of custody; verification (`--verify`) re-reads the source against the image after acquisition. Multiple inputs can be imaged in one invocation, and output can be segmented into fixed-size parts. The maintained edition of this classic tool lives at github.com/Obsidian-Covenant/aimage; it depends on the afflib package.

## Typical use cases

### Image a device into an AFF file

Acquire a disk with compression and hashing built in.

```
aimage /dev/sdb evidence.aff
```

Outcome: reads `/dev/sdb` and writes `evidence.aff`, compressing pages and computing image hashes by default; interactive progress/statistics show throughput and completion. Omit the output to preview behavior, and pass `-` or `/dev/stdin` as INPUT to image a stream.

### Verify the image against the source

Add a chain-of-custody check to acquisition.

```
aimage -b /dev/sdb evidence.aff
```

Outcome: after imaging, `--verify` re-reads the input device and compares it against the written AFF image, reporting any mismatch before you leave the scene.

### Split the image into fixed-size segments

Keep output files at a manageable or media-fitted size.

```
aimage -M 700m /dev/sdb evidence.aff
```

Outcome: segments the output so no file exceeds 700 MB (`-M` accepts g/m/k/b suffixes, or `cd`/`dvd`/`dvddl` shorthand); the set of segments forms one segmented AFF image that the afflib tools treat as a single image.

### Embed case metadata in the image

Record acquisition details inside the evidence file itself.

```
aimage -g case=2026-0114 -g examiner=user /dev/sdb evidence.aff
```

Outcome: creates named AFF metadata segments holding the case number and examiner, stored in the image alongside the data and included in later `affinfo` output.

### Image a remote stream

Acquire over the network without intermediate storage.

```
aimage listen:9001 evidence.aff
```

Outcome: aimage listens on TCP port 9001 and images whatever bytes are sent to it (e.g. `dd if=/dev/sdb | nc sender-ip 9001` from the remote host) — useful when the acquiring machine cannot mount the source device.
