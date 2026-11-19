---
tool: "keimpx"
build-system: "scripts"
upstream: "http://code.google.com/p/keimpx/"
recipe: "blackarch"
source: "http://code.google.com/p/keimpx/"
license: "Apache"
blackarch-pkgver: "300.37190f4"
---

# Installing keimpx

> Tool to verify the usefulness of credentials across a network over SMB.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/keimpx) (version `300.37190f4`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S keimpx    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://code.google.com/p/keimpx/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `distribute`, `pycryptodome`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[keimpx]] · [[BlackArch Tools]]

