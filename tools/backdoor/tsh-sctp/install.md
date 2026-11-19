---
tool: "tsh-sctp"
build-system: "scripts"
upstream: "https://github.com/infodox/tsh-sctp"
recipe: "blackarch"
source: "https://github.com/infodox/tsh-sctp"
license: "GPL"
blackarch-pkgver: "2.850a2da"
---

# Installing tsh-sctp

> An open-source UNIX backdoor.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tsh-sctp) (version `2.850a2da`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S tsh-sctp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/infodox/tsh-sctp) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[tsh-sctp]] · [[BlackArch Tools]]

