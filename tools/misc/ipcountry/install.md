---
tool: "ipcountry"
build-system: "scripts"
upstream: "https://nullsecurity.net/tools/misc.html"
recipe: "blackarch"
source: "https://nullsecurity.net/tools/misc.html"
license: "custom:unknown"
blackarch-pkgver: "2.3"
---

# Installing ipcountry

> Fetches IPv4 ranges of given country in host and cidr format.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ipcountry) (version `2.3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ipcountry    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://nullsecurity.net/tools/misc.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[ipcountry]] · [[BlackArch Tools]]

