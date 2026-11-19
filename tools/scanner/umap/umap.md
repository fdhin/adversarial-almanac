---
name: "umap"
categories:
  - blackarch-scanner
version: "25.3ad8121"
homepage: "https://github.com/nccgroup/umap"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: AGPL
meta-checked: "2026-11-09"
github-repo: "nccgroup/umap"
github-stars: 295
github-pushed: "2018-11-28"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# umap

> USB host security assessment and fuzzing tool driven by Facedancer hardware.

## What it's for

umap (NCC Group, Andy Davis) tests the USB host side — the stack inside a PC, tablet or appliance — by emulating USB devices through a Facedancer board: it enumerates which device classes a host supports, presents itself as an arbitrary class with a specific VID/PID/revision, fingerprints the host operating system, and fuzzes enumeration and class-specific behavior with test cases derived from USB standards and known bug patterns. It is not software-only: a Facedancer board attached to a serial port is mandatory (`-P`), it must run as root with pyserial installed, and emulation is slow compared with real hardware by nature of the board. BlackArch wraps it as `umap` (python3 umap.py).

## Typical use cases

### List the device classes umap can emulate

```bash
umap -L
```

Outcome: prints the class:subclass:protocol triplets (e.g. 06:01:01, still image capture) that the identification, emulation and fuzzing options accept — no hardware needed.

### Identify which device classes a host supports

```bash
umap -P /dev/ttyUSB0 -i
```

Outcome: presents each supported device class to the connected host through the Facedancer and prints a table marking each one SUPPORTED or not.

### Emulate a specific device with a chosen VID/PID

```bash
umap -P /dev/ttyUSB0 -e 06:01:01 -v 04da -p 2374
```

Outcome: connects a virtual still-image device carrying the given Vendor/Product ID to the host, triggering its real driver and application stack (for example an image import tool opening); `-R 04da:2374` looks up what a VID/PID pair corresponds to before emulating it.

### Fuzz a device class

```bash
umap -P /dev/ttyUSB0 -f 01:01:00:C
```

Outcome: after first confirming the host supports the class, fires the test-case set at the host stack — E for enumeration only, C for class-specific cases, A for all — while `-s 01:01:00:E:6` runs a single shot starting at test case 6.

### Fingerprint the host operating system

```bash
umap -P /dev/ttyUSB0 -O
```

Outcome: performs USB-side operating system identification of the connected host and prints the fingerprint result.

*No corpus evidence — derived from upstream documentation (nccgroup/umap README and the project wiki's umap-documentation page).*
