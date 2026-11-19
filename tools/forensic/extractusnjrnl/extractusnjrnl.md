---
name: "extractusnjrnl"
categories:
  - blackarch-forensic
version: "7.362d4290"
homepage: "https://github.com/jschicht/ExtractUsnJrnl"
packaged-by: [blackarch]
blackarch-builddate: "2019-11-23"
build-system: scripts
license: CCPL
meta-checked: "2026-11-09"
github-repo: "jschicht/ExtractUsnJrnl"
github-stars: 112
github-pushed: "2019-07-30"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# extractusnjrnl

> Windows utility that extracts the NTFS USN change journal ($UsnJrnl:$J) from a live device or a raw image, skipping the sparse padding other dumps include.

## What it's for

The NTFS change journal lives in the hidden metafile `$Extend\$UsnJrnl`, with the actual records in the alternate data stream `$J`. That stream is typically sparse, so extracting it naively produces a huge file that is mostly zeroes. ExtractUsnJrnl pulls out only the real journal data, which makes both the extraction faster and the output far smaller, ready for parsing with any USN journal analyzer. It runs on Windows (shipped as `ExtractUsnJrnl.exe` / `ExtractUsnJrnl64.exe`) and reads either a raw dd-style disk or partition image or a live device path, including volumes and shadow copies that have no drive letter mounted. It only extracts the journal — record interpretation is left to other tooling.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Extract the journal from a live volume

```
ExtractUsnJrnl /DevicePath:c:
```

Outcome: writes the `$J` data of the C: volume to `$UsnJrnl_$J.bin` in the program directory (an existing file of that name is renamed with a timestamp first).

### Extract the journal from a raw image

```
ExtractUsnJrnl /ImageFile:e:\images\disk.dd /ImageVolume:2 /OutputPath:e:\temp /OutputName:UsnJrnl_vol2.bin
```

Outcome: reads volume 2 out of the image file and writes its non-sparse journal data to `e:\temp\UsnJrnl_vol2.bin` for offline parsing.

### Extract from a volume shadow copy

```
ExtractUsnJrnl /DevicePath:\\.\HarddiskVolumeShadowCopy1 /OutputPath:e:\temp
```

Outcome: dumps the journal of the shadow copy to `e:\temp` (default name `$UsnJrnl_$J.bin`), useful to compare the journal at an earlier point in time.

### Extract from a whole physical drive

```
ExtractUsnJrnl /DevicePath:\\.\PhysicalDrive0 /ImageVolume:3 /OutputPath:e:\temp
```

Outcome: reads volume 3 of the physical drive directly and extracts its journal, without needing the volume to be mounted.
