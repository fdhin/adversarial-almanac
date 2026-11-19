---
tool: "dissector"
build-system: "other"
upstream: "http://packetstormsecurity.com/files/125972/Coloured-ELF-File-Dissector.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/UNIX/utilities/dissector.c"
license: "custom:unknown"
blackarch-pkgver: "1"
---

# Installing dissector

> This code dissects the internal data structures in ELF files. It supports x86 and x86_64 archs and runs under Linux.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dissector) (version `1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dissector    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/125972/Coloured-ELF-File-Dissector.html) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
gcc -Wall "$pkgname.c" -o $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[dissector]] · [[BlackArch Tools]]

