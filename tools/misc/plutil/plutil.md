---
name: "plutil"
categories:
  - blackarch-misc
version: "1.6"
homepage: "http://scw.us/iPhone/plutil/"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-19"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# plutil

> Single-file Perl reimplementation of the macOS plutil utility that converts Apple .plist files between binary and editable XML form.

## What it's for

Apple property lists ship either as XML text or as a compact binary format ("bplist"), and extracted iOS/macOS artifacts are frequently in the binary form that text editors cannot show. plutil.pl, by Pete M. Wilson (hosted at scw.us), converts in both directions with nothing but Perl installed: it sniffs the file's magic and converts a binary plist to UTF-8 XML, or an XML plist back to binary. It deliberately does not implement the macOS plutil command-line syntax — the only argument it accepts is one file name. Known limits: float precision output in binary format is never used, and the null, fill and uid object types are not translated. The BlackArch package installs it as `plutil.pl` (not `plutil`) to avoid colliding with the unrelated plutil binary from gnustep-base.

## Typical use cases

### Convert a binary plist to editable XML

```
plutil.pl Info.plist
```

Outcome: detects the `bplist` magic, converts the file to UTF-8 XML, and writes the result next to the input with the type token swapped in the name — a binary `Info.plist` becomes `Info.text.plist` — leaving the original untouched and ready to open in any editor.

### Convert an edited XML plist back to binary

```
plutil.pl OutputInfo.text.plist
```

Outcome: detects XML text input and writes a binary plist copy named `OutputInfo.binary.plist`, suitable for placing back onto an Apple filesystem. Without an existing file argument the script just prints its one-line usage.

*No corpus evidence — derived from upstream documentation.*
