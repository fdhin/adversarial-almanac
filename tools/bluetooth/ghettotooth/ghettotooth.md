---
name: "ghettotooth"
categories:
  - blackarch-bluetooth
version: "1.0"
homepage: "http://www.oldskoolphreak.com/tfiles/ghettotooth.txt"
packaged-by: [blackarch]
blackarch-builddate: "2022-05-31"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ghettotooth

> Minimal Perl "ghettodriving" loop that repeatedly runs hcitool scan and logs every discoverable device that drifts into range.

## What it's for

War-driving-style Bluetooth logging with no dependencies beyond Perl and the BlueZ `hcitool`. Given an interface name it loops `hcitool -i <hciX> scan` forever, strips the `Scanning ...` header from each pass, and echoes the device lines both to the terminal and to a timestamped log file `ghettotooth-<YYYY-MM-DD-HH:MM>` created in the working directory, so the file doubles as a record of when each device was in range. It sees only discoverable devices — it performs no probing, no address brute force, and has no Bluetooth Low Energy support. Anything not matching `hci<N>` (e.g. `eth0`) just prints the usage summary.

## Typical use cases

### Log discoverable devices over time

```
sudo ghettotooth hci0
```

Outcome: prints the banner, then continuously repeats an inquiry scan on `hci0`; every device line is printed and appended to `ghettotooth-<date>` in the current directory. Stop with Ctrl+C — the interrupt handler closes the log cleanly. Compare the log's timestamps later to see which devices reappeared and when.

*No corpus evidence — derived from upstream documentation.*
