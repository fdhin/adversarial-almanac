---
name: "imhex"
categories:
  - blackarch-misc
version: "nightly.r0.g34483cb"
homepage: "https://github.com/WerWolv/ImHex"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: cmake
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "WerWolv/ImHex"
github-stars: 55771
github-pushed: "2026-10-09"
github-archived: false
github-issues: 423
github-match: "exact"
gh-checked: "2026-11-07"
worth: 9.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 772
gh-activity-checked: "2026-11-09"
---

# imhex

> Reverse-engineering hex editor with a pattern language — and a real set of CLI utilities inside the GUI binary.

*No corpus evidence — derived from upstream documentation.*

## What it's for

ImHex (by WerWolv) is a hex editor built for reverse engineers: the Pattern Language declaratively decodes structures onto raw bytes, and the editor adds disassembly, YARA rules, data analysis, a node-based file patcher and a plugin API. It is primarily a GUI application, but the same binary exposes headless subcommands — hashing, hexdumping, file-type identification via the magic database, math evaluation, a full Pattern Language CLI and an MCP server — so many binary-inspection jobs run without opening a window. Files given as bare arguments open in the GUI (implicit `--open`); anything following a subcommand flag belongs to that subcommand. Run `imhex --help` for the complete, live subcommand table.

## Typical use cases

### Open files in the GUI

Start a reverse-engineering session with one or more binaries.

```
imhex firmware.bin
```

Outcome: the ImHex window opens with `firmware.bin` loaded; if an instance is already running, the file is forwarded to it instead of starting a second window.

### Hash a file without opening the GUI

Compute a checksum from the shell.

```
imhex --hash sha256 firmware.bin
```

Outcome: prints the SHA-256 digest of the file and exits. Available algorithms: md5, sha1, sha224, sha256, sha384, sha512.

### Hexdump a slice of a file

Get a canonical dump of one region without loading the editor.

```
imhex --hexdump firmware.bin 0 256
```

Outcome: prints a hex dump of the first 256 bytes at offset 0. Offset and size are optional (defaults: 0 and the whole file) and accept `0x`-prefixed hex values.

### Identify an unknown file

Use the magic database instead of `file`.

```
imhex --magic desc sample.bin
```

Outcome: prints the human-readable description derived from the file's magic signature; `--magic mime sample.bin` returns the MIME type instead.

### Run the Pattern Language CLI

Work with pattern files headlessly.

```
imhex --pl
```

Outcome: with no further arguments this prints the Pattern Language CLI's own usage — the entry point for validating and evaluating `.hexpat` patterns outside the GUI.

### Expose ImHex to an AI client over MCP

Start the built-in MCP interface.

```
imhex --mcp
```

Outcome: runs an MCP server over stdin/stdout — the process an MCP-capable AI client launches to inspect and manipulate data with ImHex; it exits when the client disconnects.
