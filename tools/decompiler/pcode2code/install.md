---
tool: "pcode2code"
build-system: "scripts"
upstream: "https://github.com/Big5-sec/pcode2code"
recipe: "blackarch"
source: "https://github.com/Big5-sec/pcode2code"
license: "GPL3"
blackarch-pkgver: "6.65ae983"
---

# Installing pcode2code

> VBA p-code decompiler.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pcode2code) (version `6.65ae983`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pcode2code    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Big5-sec/pcode2code) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pcodedmp`, `pip`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `dos2unix`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[decompiler]] · [[pcode2code]] · [[BlackArch Tools]]

