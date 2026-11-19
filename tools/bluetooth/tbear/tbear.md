---
name: "tbear"
categories:
  - blackarch-bluetooth
version: "1.5"
homepage: "http://freshmeat.net/projects/t-bear"
packaged-by: [blackarch]
blackarch-builddate: "2019-08-24"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# tbear

> T-BEAR: a 2005 Bluetooth auditing suite — ncurses scanner, hidden-device locator, and L2CAP flood stress tool.

## What it's for

T-BEAR (Transient Bluetooth Environment Auditor) is a small suite for auditing classic Bluetooth environments, shipping three programs: `tbear`, an ncurses-based device scanner in the spirit of kismet; `tbsearch`, which locates hidden (non-discoverable) devices by systematically probing address ranges derived from the OUI database installed at `/etc/btoui`; and `tanya`, an L2CAP packet flood used to stress-test device stacks. It dates from the BlueZ-of-2005 era and knows nothing about BLE; its DoS component is only appropriate against equipment you are authorized to test. It remains packaged and buildable, so it still serves as a lightweight, dependency-free way to walk a range of Bluetooth addresses.

## Typical use cases

### Run the ncurses environment scanner

```
tbear -i hci0 -l scan.log -b
```

Outcome: a live ncurses display of Bluetooth devices found in range; each discovery is appended to scan.log and a beep sounds on each find. `tbear -h` prints the option summary.

### Hunt for hidden devices across several adapters

```
tbsearch hci0 hci1
```

Outcome: one probing thread per listed adapter walks the OUI address space from `/etc/btoui` and reports non-discoverable devices that respond — the multi-adapter form is the main speed lever, since the sweep is slow by nature.

### Resume a hidden-device sweep from a known address

```
tbsearch -b 00:11:22:33:44:55 hci0
```

Outcome: the sweep starts from the given BD_ADDR instead of the top of the OUI list, letting an interrupted search continue where it stopped rather than beginning again.

### Stress-test a device's L2CAP stack

```
tanya -s 600 00:11:22:33:44:55
```

Outcome: floods the target with L2CAP packets of the size set by `-s` as fast as the adapter allows; a fragile stack stalls or drops until reset, which is the robustness signal. Run `tanya` with no arguments to confirm the usage line before each session.

*No corpus evidence — derived from upstream documentation.*
