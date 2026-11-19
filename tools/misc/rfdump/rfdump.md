---
name: "rfdump"
categories:
  - rfid
version: "1.6-6"
homepage: "http://www.rfdump.org"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2024-07-13"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
worth: 1.1
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# rfdump

> GTK application for reading, editing and cloning RFID tag memory through a serially (or TCP) connected reader such as the ACG Multi-Tag Reader.

## What it's for

rfdump drives an RFID reader (tested with the ACG Multi-Tag Reader in ASCII mode, 9600 baud 8N1; a TCP stream to the reader works too via `hostname:port` in the preferences) to detect tags and show their metadata — tag ID, type, manufacturer — and to display the tag's data memory page by page in hex and ASCII. Pages can be edited inline and are written straight back to the tag, and whole tag contents save and load as XML, which effectively clones data from one tag to another. A "cookie" feature demonstrates RFID-based customer tracking by planting a configurable ID (default `DEADFACE`) with an auto-incrementing counter. Physical reader hardware is required — there is no simulation mode — and nearly all interaction happens in the GUI; the binary itself takes only a few launch flags.

## Typical use cases

### Launch against a serial reader

Point the tool at the reader device instead of editing preferences first.

```
rfdump -p /dev/ttyUSB0
```

Outcome: opens the GUI connected to the reader on `/dev/ttyUSB0` and starts scanning; detected tags appear with type and ID, and their memory is read into the hex/ASCII table.

### Run the reader setup pass at startup

Re-initialize the reader and tag-type tables before scanning.

```
rfdump --setupreader
```

Outcome: performs the reader setup step during startup (equivalent to `-setupreader`), useful when the reader was reconfigured or switched since the last run.

### Reload a previously saved tag image

Bring a stored tag dump back into the editor.

```
rfdump tag-backup.xml
```

Outcome: any argument ending in `.xml` is loaded at initialization, restoring the saved tag's metadata and memory pages so they can be inspected or written to a new tag.

*No corpus evidence — derived from upstream documentation.*
