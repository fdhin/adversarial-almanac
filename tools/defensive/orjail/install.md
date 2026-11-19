---
tool: "orjail"
build-system: "scripts"
upstream: "https://github.com/orjail/orjail"
recipe: "blackarch"
aur-package: "orjail"
source: "https://github.com/orjail/orjail"
license: "WTFPL"
blackarch-pkgver: "200.ae38ba2"
---

# Installing orjail

> A more secure way to force programs to exclusively use tor network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/orjail) (version `200.ae38ba2`, license WTFPL).

## Arch / BlackArch

```bash
sudo pacman -S orjail    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/orjail/orjail) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `bc`
  - `firejail`
  - `iptables`
  - `tor`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[defensive]] · [[orjail]] · [[BlackArch Tools]]

