---
name: "obexstress"
categories:
  - blackarch-bluetooth
version: "0.1"
homepage: "http://bluetooth-pentest.narod.ru/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# obexstress

> Interactive OBEX service prober: automated directory-traversal attempts plus malformed- and overlong-filename DoS tests.

## What it's for

Auditing the OBEX file-transfer service of a phone or other Bluetooth device for weaknesses that show up in sloppy implementations (Dmitry, bluetooth-pentest.narod.ru). Given a BD address and channel it connects via the `lightblue` library and drives the service from a menu: an automated command suite that walks `ls` / `cd ..` / `cd ../../../` / `mkdir` / `put` / `get` / `rm` and a put with an `../../../../../`-prefixed filename to look for directory traversal, plus fuzzing modes that send files whose names contain single special characters (space, TAB, `:`, `/`, `?`, backslash, punctuation runs, alphanumeric) or a chosen length of `A` characters, either of which can crash vulnerable firmwares. It is a Python 2 era tool: running it today requires a python2 environment with `lightblue`/`pybluez` available. Note the tool's own warning that some OBEX services answer `OK` to traversal requests without acting on them, so positive responses can be false positives.

## Typical use cases

### Exercise an OBEX service end to end

```
obexstress AA:BB:CC:DD:EE:01 9
```

Outcome: connects to the OBEX service on the given address and channel and opens the menu. Choose `o` to run the full command/traversal suite — each request and its OBEX response code is printed, with folder listings shown where the `x-obex/folder-listing` get succeeds. Choose `m` (or keys `1`–`9` for preset characters) to send single malformed-filename puts, `l` to send a filename of a chosen number of `A` characters, and `x` to exit; watch response codes and whether the service stays up — a dropped connection or timeout indicates the filename crashed it.

*No corpus evidence — derived from upstream documentation.*
