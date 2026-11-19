---
tool: "cudahashcat"
build-system: "scripts"
upstream: "http://hashcat.net/oclhashcat/"
recipe: "blackarch"
source: "http://hashcat.net/oclhashcat/"
license: "custom"
blackarch-pkgver: "2.01"
---

# Installing cudahashcat

> Worlds fastest WPA cracker with dictionary mutation engine.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cudahashcat) (version `2.01`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S cudahashcat    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://hashcat.net/oclhashcat/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[cudahashcat]] · [[BlackArch Tools]]

