---
tool: "firefox-decrypt"
build-system: "scripts"
upstream: "https://github.com/unode/firefox_decrypt"
recipe: "blackarch"
source: "https://github.com/unode/firefox_decrypt"
license: "GPL3"
blackarch-pkgver: "1.1.3.r4.g1740363"
---

# Installing firefox-decrypt

> Extract passwords from Mozilla Firefox, Waterfox, Thunderbird, SeaMonkey profiles.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/firefox-decrypt) (version `1.1.3.r4.g1740363`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S firefox-decrypt    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/unode/firefox_decrypt) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pysqlite3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `nss` — Debian libnss3-dev / Fedora nss-devel
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[firefox-decrypt]] · [[BlackArch Tools]]

