---
tool: "thc-keyfinder"
build-system: "other"
upstream: "https://www.thc.org/releases.php"
recipe: "blackarch"
source: "https://www.thc.org/releases/keyfinder.c"
license: "GPL2"
blackarch-pkgver: "1.0"
provides: ["keyfinder"]
---

# Installing thc-keyfinder

> Finds crypto keys, encrypted data and compressed data in files by analyzing the entropy of parts of the file.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/thc-keyfinder) (version `1.0`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S thc-keyfinder    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.thc.org/releases.php) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `gcc`

## Exact build steps from the recipe

```bash
gcc -o keyfinder keyfinder.c -lm
```

## Installed layout (per recipe)

- Commands: `/usr/bin/keyfinder`

Backlinks: [[cracker]] · [[thc-keyfinder]] · [[BlackArch Tools]]

