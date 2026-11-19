---
tool: "cafebabe"
build-system: "scripts"
upstream: "https://github.com/GraxCode/Cafebabe/releases/"
recipe: "blackarch"
source: "https://github.com/GraxCode/Cafebabe/releases/"
license: "GPL3"
blackarch-pkgver: "0.1.2"
---

# Installing cafebabe

> Java bytecode editor & decompiler.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cafebabe) (version `0.1.2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S cafebabe    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://grax.info/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[decompiler]] · [[cafebabe]] · [[BlackArch Tools]]

