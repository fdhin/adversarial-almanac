---
tool: "storm-ring"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/115852/Storm-Ringing-PABX-Test-Tool.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/115852/Storm-Ringing-PABX-Test-Tool.html"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing storm-ring

> This simple tool is useful to test a PABX with "allow guest" parameter set to "yes" (in this scenario an anonymous caller could place a call).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/storm-ring) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S storm-ring    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/115852/Storm-Ringing-PABX-Test-Tool.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[voip]] · [[storm-ring]] · [[BlackArch Tools]]

