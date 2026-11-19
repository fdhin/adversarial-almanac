---
tool: "osi.ig"
build-system: "scripts"
upstream: "https://github.com/th3unkn0n/osi.ig"
recipe: "blackarch"
source: "https://github.com/th3unkn0n/osi.ig"
license: "custom:unknown"
blackarch-pkgver: "101.4debaa2"
---

# Installing osi.ig

> Instagram OSINT Tool gets a range of information from an Instagram account.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/osi.ig) (version `101.4debaa2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S osi.ig    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/th3unkn0n/osi.ig) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `certifi`, `chardet`, `idna`, `requests`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[osi.ig]] · [[BlackArch Tools]]

