---
name: "streams"
gentoo-category: "app-forensics"
version: "0.1.0"
homepage: "http://src.carnivore.it/streams/about"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: GPL-2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# streams

> Interactive shell for browsing, mining and processing TCP streams in pcap files — Tillmann Werner's session-level capture explorer (Honeynet Project, 2011).

## What it's for

streams (v0.1.0, by Tillmann Werner of the Honeynet Project's Giraffe chapter, 2011) reassembles the TCP sessions inside a libpcap capture file and gives you a command prompt over them: filter what gets parsed (BPF expressions) and what gets listed (content patterns, empty/incomplete sessions), inspect each session's timing and size, dump a selected session's reassembled bytes to a file, or pipe them straight through an external program for decoding. It is built for triaging large pcaps at session level — find the one conversation that matters, then extract or transform just its data — instead of scrolling raw packet lists. It is a Linux/UNIX command-line tool (libpcap- and readline-based, GPL-2) and has nothing to do with Sysinternals' NTFS alternate-data-streams utility of the same name. The original upstream at carnivore.it is gone — the domain has lapsed into parking — so pentoo's 0.1.0 ebuild is the practical distribution and the archived project README documents the session commands used below.

## Typical use cases

### Analyze a capture and list its sessions

```
streams
streams> analyze capture.pcap
streams> list
```

Outcome: the trace file is parsed and reassembled into TCP sessions (a status line like "file processed, 4 streams (2 non-empty and complete)"); `list` then prints one line per session — index, relative timestamps, both endpoints with ports, and byte counts — so you pick the session to work on by its index.

### Restrict parsing to traffic of interest

```
streams> bpf port 80
streams> analyze capture.pcap
```

Outcome: sets a Berkeley packet filter expression that is applied when trace files are analyzed, so only matching packets become sessions; `count` then reports the number of streams that survived.

### Toggle empty and incomplete sessions in listings

```
streams> filter
streams> list
```

Outcome: toggles the stream filter — with it active, empty and incomplete sessions are excluded from `list`; toggled off, every parsed session appears, incomplete ones marked `[incomplete]`.

### Select sessions by content

```
streams> match User-Agent:
```

Outcome: records a content pattern that stream selection is based on (`match x <pattern>` takes the pattern in hexadecimal encoding); the listing and dump commands then operate on the sessions containing it.

### Dump a session's reassembled data

```
streams> outfile session.bin
streams> dump 2
```

Outcome: writes the reassembled bytes of stream 2 (the index shown by `list`) to `session.bin`, ready for inspection with hex editors, string tools or decoders.

### Pipe a session through an external program

```
streams> ext gzip -c
streams> pipe 3
```

Outcome: runs the external program (with its arguments) on the selected session's data — `pipe 3` feeds stream 3 through it — so captured payloads can be decompressed or decoded on the fly without writing intermediate files.

*No corpus evidence — derived from upstream documentation (carnivore.it README preserved via the Internet Archive) and the pentoo ebuild.*
