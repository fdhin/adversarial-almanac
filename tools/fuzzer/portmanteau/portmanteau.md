---
name: "portmanteau"
categories:
  - blackarch-fuzzer
version: "1.0"
homepage: "https://packetstormsecurity.com/files/134230/Portmanteau-Unix-Driver-IOCTL-Security-Tool.html"
packaged-by: [blackarch]
blackarch-builddate: "2023-03-01"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# portmanteau

> Experimental Linux driver-ioctl tool: imports a driver's ioctl set from kernel headers into SQLite, then fuzzes the device node and emits standalone C PoCs.

## What it's for

Portmanteau (Jeremy Brown, October 2015) maps the local IOCTL attack surface that device drivers expose to unprivileged users. It parses kernel/driver header files for ioctl definitions and stores them (name, macro, buffer type) in a local SQLite database (`drsig.db`, created empty on first run), lets you map each signature to its real device path under /dev, and then fuzzes a device's ioctls with random generational inputs — automatically generating compilable C proof-of-concept programs for each case so kernel panics can be reproduced and analyzed. Only 32-bit x86 Linux was tested, and the author warns it can panic the box it runs on: treat it as experimental and run it on a disposable VM. Manual database edits are supported with colon-separated add/update/delete flags, or directly through the sqlite3 client.

## Typical use cases

### List imported driver signatures

```
portmanteau
```

Outcome: prints every ioctl signature in drsig.db as a device_name / ioctl_name / ioctl_macro table (plus a usage hint); on first run it silently creates an empty database ready for imports.

### Import ioctls from a kernel header

```
portmanteau -i /usr/src/kernels/$(uname -r)/include/uapi/linux/if_tun.h
```

Outcome: extracts each ioctl definition (TUNSETIFF, TUNSETDEBUG, ...) from the header into the database, printing one "Adding found ioctl" line per entry; the device_name column starts as an arbitrary hash until you set a real path.

### Point a signature at its device node

```
portmanteau -u "device_name:6955F2B0AD:/dev/net/tun"
```

Outcome: updates the signature row so fuzzing opens the real device node instead of the import-time hash; `-a` and `-d` add and delete signatures in the same colon-separated format.

### Fuzz a device's ioctls

```
portmanteau -D /dev/net/tun -N 100 -z
```

Outcome: opens /dev/net/tun and fires 100 iterations of fuzzed data at every database ioctl registered for that device, printing each ioctl name and number as it is hit; a compilable C PoC per case is written under poc/ (only the last two are kept) for crash reproduction.

*No corpus evidence — derived from upstream documentation.*
