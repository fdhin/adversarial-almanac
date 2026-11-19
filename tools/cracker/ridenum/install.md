---
tool: "ridenum"
build-system: "scripts"
upstream: "https://github.com/trustedsec/ridenum"
recipe: "blackarch"
source: "https://github.com/trustedsec/ridenum"
license: "custom"
blackarch-pkgver: "75.9e3b89b"
---

# Installing ridenum

> A null session RID cycle attack for brute forcing domain controllers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ridenum) (version `75.9e3b89b`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S ridenum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/trustedsec/ridenum) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pexpect`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[ridenum]] · [[BlackArch Tools]]

