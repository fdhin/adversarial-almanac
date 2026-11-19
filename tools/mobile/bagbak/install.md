---
tool: "bagbak"
build-system: "node"
upstream: "https://github.com/ChiChou/bagbak"
recipe: "blackarch"
source: "https://github.com/ChiChou/bagbak"
license: "Apache"
blackarch-pkgver: "344.39621f9"
---

# Installing bagbak

> Yet another frida based App decryptor.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bagbak) (version `344.39621f9`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S bagbak    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `nodejs` + `npm`):**

**Source:** download the release from the [upstream site](https://github.com/ChiChou/bagbak) (the recipe uses a versioned archive).

```bash
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `git`, `nodejs`, `npm`
- **System libraries** (Arch name — translate for your distro):
  - `zip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[bagbak]] · [[BlackArch Tools]]

