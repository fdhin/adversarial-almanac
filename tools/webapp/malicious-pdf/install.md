---
tool: "malicious-pdf"
build-system: "scripts"
upstream: "https://github.com/jonaslejon/malicious-pdf"
recipe: "blackarch"
source: "https://github.com/jonaslejon/malicious-pdf"
license: "BSD-2-Clause"
blackarch-pkgver: "v1.0.1.r5.g3409ac7"
---

# Installing malicious-pdf

> Generate a bunch of malicious pdf files with phone-home functionality.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/malicious-pdf) (version `v1.0.1.r5.g3409ac7`, license BSD-2-Clause).

## Arch / BlackArch

```bash
sudo pacman -S malicious-pdf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/jonaslejon/malicious-pdf) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[malicious-pdf]] · [[BlackArch Tools]]

