---
name: "ntfs-file-extractor"
categories:
  - blackarch-forensic
version: "6.f2b23d72"
homepage: "https://github.com/jschicht/NtfsFileExtractor"
packaged-by: [blackarch]
blackarch-builddate: "2019-11-23"
build-system: scripts
license: CCPL
meta-checked: "2026-11-09"
github-repo: "jschicht/NtfsFileExtractor"
github-stars: 22
github-pushed: "2014-11-01"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ntfs-file-extractor

> GUI Windows tool (AutoIt) that pulls files straight off NTFS structures — locked system files, deleted entries, ADS — from volumes, images, physical drives and shadow copies.

## What it's for

NtfsFileExtractor (Joakim Schicht) extracts files from an NTFS filesystem by parsing the volume's structures directly rather than going through the Windows file APIs. Sources are a mounted volume, a disk or partition image (MBR and GPT), direct `\\.\PhysicalDriveN` access, or volume shadow copies it detects itself. It handles resident, non-resident, compressed and sparse data as well as alternate data streams (ADS written out as `basefile.ext[ADS_adsname.ext]`), and offers four selection modes: all files, active only, deleted only, or files picked by MFT index number — index 0 and 2 are $MFT and $LogFile. Because it reads the raw filesystem it bypasses filesystem access controls, reaching files no normal process can open (SAM/SYSTEM hives, pagefile, the NTFS metafiles). Deleted extractions are named with a `[DEL+<index>]` prefix so they cannot be confused with live files. The extracted $MFT can then be decoded with a record parser such as [[analyzemft]].

## No command-line interface

The packaged build is a Windows GUI application — AutoIt source in the repository with prebuilt 32-/64-bit executables, driven entirely by its window (comboboxes to pick the volume, buttons to rescan mounted drives, scan physical drives, scan shadow copies and test them, a chooser for All/Deleted/Active/User Select, an output-directory picker and Start Extraction); the source contains no command-line handling, so there are no runnable examples to give. On BlackArch `/usr/bin/ntfs-file-extractor` is a Wine launcher around the packaged executable, so the same GUI runs under Wine on Linux.

*No corpus evidence — derived from upstream documentation.*
