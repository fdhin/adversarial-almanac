---
tool: "jwscan"
build-system: "scripts"
upstream: "https://github.com/katjahahn/JWScan"
recipe: "blackarch"
source: "https://github.com/katjahahn/JWScan"
license: "Apache"
blackarch-pkgver: "7.874b3a5"
---

# Installing jwscan

> Scanner for Jar to EXE wrapper like Launch4j, Exe4j, JSmooth, Jar2Exe.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jwscan) (version `7.874b3a5`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S jwscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/katjahahn/JWScan.git
cd JWScan
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `sbt`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[reversing]] · [[jwscan]] · [[BlackArch Tools]]

