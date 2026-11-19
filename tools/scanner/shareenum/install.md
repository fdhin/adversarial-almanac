---
tool: "shareenum"
build-system: "scripts"
upstream: "https://github.com/CroweCybersecurity/shareenum"
recipe: "blackarch"
source: "https://github.com/CroweCybersecurity/shareenum"
license: "GPL3"
blackarch-pkgver: "48.db728dd"
---

# Installing shareenum

> Tool to enumerate shares from Windows hosts.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shareenum) (version `48.db728dd`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S shareenum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/CroweCybersecurity/shareenum) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `samba`
  - `tdb`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[shareenum]] · [[BlackArch Tools]]

