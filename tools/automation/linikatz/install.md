---
tool: "linikatz"
build-system: "scripts"
upstream: "https://github.com/portcullislabs/linikatz"
recipe: "blackarch"
source: "https://github.com/portcullislabs/linikatz"
license: "BSD"
blackarch-pkgver: "54.b06ec5f"
---

# Installing linikatz

> Tool to attack AD on UNIX.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/linikatz) (version `54.b06ec5f`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S linikatz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/portcullislabs/linikatz) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[linikatz]] · [[BlackArch Tools]]

