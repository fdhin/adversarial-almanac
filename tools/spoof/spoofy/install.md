---
tool: "spoofy"
build-system: "scripts"
upstream: "https://github.com/MattKeeley/Spoofy"
recipe: "blackarch"
source: "https://github.com/MattKeeley/Spoofy"
license: "CC0-1.0"
blackarch-pkgver: "v1.0.3.r10.g8e6982e"
---

# Installing spoofy

> Check if a list of domains can be spoofed based on SPF and DMARC records.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/spoofy) (version `v1.0.3.r10.g8e6982e`, license CC0-1.0).

## Arch / BlackArch

```bash
sudo pacman -S spoofy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/MattKeeley/Spoofy) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `dnspython`, `openpyxl`, `pandas`, `tldextract`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[spoof]] · [[spoofy]] · [[BlackArch Tools]]

