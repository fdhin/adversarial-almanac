---
tool: "userrecon"
build-system: "scripts"
upstream: "https://github.com/thelinuxchoice/userrecon"
recipe: "blackarch"
source: "https://github.com/thelinuxchoice/userrecon"
license: "GPL3"
blackarch-pkgver: "10.3b56891"
---

# Installing userrecon

> Find usernames across over 75 social networks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/userrecon) (version `10.3b56891`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S userrecon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/thelinuxchoice/userrecon) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `curl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[userrecon]] · [[BlackArch Tools]]

