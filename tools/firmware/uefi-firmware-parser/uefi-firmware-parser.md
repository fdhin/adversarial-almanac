---
name: "uefi-firmware-parser"
categories:
  - blackarch-firmware
version: "215.c4b8c64"
homepage: "https://github.com/theopolis/uefi-firmware-parser"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "theopolis/uefi-firmware-parser"
github-stars: 933
github-pushed: "2026-11-02"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 35
gh-activity-checked: "2026-11-09"
---

# uefi-firmware-parser

> Python tool and library that parses, extracts, and patches UEFI-related firmware — firmware volumes and capsules, Intel flash descriptors and ME regions, Dell PFS updates, and AMI Aptio capsules.

*No corpus evidence — derived from upstream documentation.*

## What it's for

The standalone `uefi_firmware` package (published on PyPI) by Teddy Reed — not to be confused with LongSoft's C++ UEFITool, which has its own built-in parser. It identifies a firmware file's format, prints its full object hierarchy (volumes, files, sections), and extracts or regenerates every object, handling Tiano/EFI and LZMA decompression via a bundled C extension. Compared to a GUI tool it is built for automation: JSON output, batch format-testing of many files, and GUID-based section/file replacement make it a natural fit for scripted firmware audits. Vendor updates wrapped in an installer (usually a PE executable) generally must be unpacked first, though Dell BIOS update executables are unwrapped automatically.

## Typical use cases

### Identify the format of firmware files

```
uefi-firmware-parser --test firmware/*
```

Outcome: prints one `path: type` line per file — values such as `EFICapsule`, `FlashDescriptor`, `IntelME`, `DellPFS`, `UEFIFirmwareVolume`, or `unknown` — a quick sort of a directory of dumps and updates before deeper parsing.

### Print the full object hierarchy of an image

```
uefi-firmware-parser bios.rom
```

Outcome: prints the parsed tree of firmware volumes, files and sections with their types and GUIDs; add `-j` for the same hierarchy as JSON instead of the tree view, and `-c` to echo each filename first when processing several files.

### Extract all volumes, files, and sections

```
uefi-firmware-parser -e -o extracted/ bios.rom
```

Outcome: writes every firmware object into `extracted/` as individual files. `-O` instead writes each input's objects to its own `<filename>_output/` folder, and `-g folder/` additionally generates an FDF (flash description file) per volume for rebuilding.

### Carve firmware structures out of an undifferentiated blob

```
uefi-firmware-parser --superbrute dump.bin
```

Outcome: tries every known format at every byte offset and parses whatever it finds — slow on large images, but recovers volumes from dumps with leading garbage or nonstandard headers. `-b` is the lighter variant that only searches the blob for firmware-volume headers.

### Replace a section or file by GUID

```
python scripts/fv_injector.py --guid 12345678-1234-1234-1234-123456789abc --injection payload.efi -o patched.rom firmware.rom
```

Outcome: replaces the object with the given GUID inside the firmware volume and writes `patched.rom`, recompressing compressed sections and updating sizes and checksums; signatures are not recomputed, so a signed image will no longer verify. Run from a checkout of the repository — the helper scripts are not installed with the package.
