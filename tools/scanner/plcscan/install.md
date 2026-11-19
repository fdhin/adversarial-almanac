---
tool: "plcscan"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/119726/PLC-Device-Scanner.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/119726/PLC-Device-Scanner.html"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing plcscan

> This is a tool written in Python that will scan for PLC devices over s7comm or modbus protocols.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/plcscan) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S plcscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/119726/PLC-Device-Scanner.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[plcscan]] · [[BlackArch Tools]]

