---
tool: "statsprocessor"
build-system: "scripts"
upstream: "https://hashcat.net/wiki/doku.php?id=statsprocessor"
recipe: "blackarch"
aur-package: "statsprocessor"
source: "https://hashcat.net/wiki/doku.php?id=statsprocessor"
license: "MIT"
blackarch-pkgver: "0.11"
kali-package: "statsprocessor"
kali-version: "0.11+git20160316-5"
---

# Installing statsprocessor

> A high-performance word-generator based on per-position Markov-attack.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/statsprocessor) (version `0.11`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S statsprocessor    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install statsprocessor    # Kali package version 0.11+git20160316-5
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://hashcat.net/wiki/doku.php?id=statsprocessor) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `p7zip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/statsprocessor`

Backlinks: [[automation]] · [[statsprocessor]] · [[BlackArch Tools]]

