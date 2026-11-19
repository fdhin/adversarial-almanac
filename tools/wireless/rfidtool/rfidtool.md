---
name: "rfidtool"
categories:
  - blackarch-wireless
version: "0.2"
homepage: "http://www.bindshell.net/tools/rfidtool.html"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-21"
build-system: make
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "Obsidian-Covenant/rfidtool"
github-stars: 0
github-pushed: "2025-12-21"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# rfidtool

> Wade Alcorn's 2005 command-line tool for reading, dumping, saving, restoring and wiping tags through a serial RFID reader.

## What it's for

rfidtool is a tiny C utility (a command line tool and library, to quote its own header) that talks to a serial-port RFID reader and performs the handful of operations that matter for tag inventory work: print the tag's contents as a hex dump, show its ID, query its size, save the tag contents to a file, write a previously saved file back to a tag, and wipe a tag to zeros. It takes exactly three arguments — device, command, optional filename — and nothing else: no protocols to configure, no dictionary attacks, no emulation. It is the kind of tool you reach for when you have a legacy serial reader and want a scriptable dump/restore loop over a stack of tags. The BlackArch package builds the Obsidian-Covenant modernized edition (gcc-current fixes) of the original bindshell.net source; behavior and interface are unchanged from 0.2.

## Typical use cases

### Dump a tag's contents and identity

See what is on the card currently on the reader.

```
rfidtool /dev/ttyUSB0 print
rfidtool /dev/ttyUSB0 id
```

Outcome: `print` reads the tag through the reader on that serial port and writes its contents to stdout as a hex dump; `id` prints just the tag's identifier and `size` its storage size — the three read-only commands, all needing only the device argument.

### Back up and restore a tag

Save a tag image, then write it back later.

```
rfidtool /dev/ttyUSB0 save tag.bin
rfidtool /dev/ttyUSB0 load tag.bin
```

Outcome: `save` reads the tag and stores the raw image in the named file; `load` later writes that file back onto a (writable) tag on the coil — together they are a clone-by-image workflow for tags the reader can write.

### Blank a tag

Zero out a writable tag before reuse.

```
rfidtool /dev/ttyUSB0 wipe
```

Outcome: overwrites the tag's contents with zeros; the command prints errors from the reader if the tag is absent or not writable, and a subsequent `print` confirms the empty state.

*No corpus evidence — derived from upstream documentation (command surface verified by compiling and running the packaged 0.2 source).*
