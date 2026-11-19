---
tool: "novahot"
build-system: "node"
upstream: "https://github.com/chrisallenlane/novahot"
recipe: "blackarch"
source: "https://github.com/chrisallenlane/novahot"
license: "BSD"
blackarch-pkgver: "23.69857bb"
---

# Installing novahot

> A webshell framework for penetration testers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/novahot) (version `23.69857bb`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S novahot    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `nodejs` + `npm`):**

**Source:** download the release from the [upstream site](https://github.com/chrisallenlane/novahot) (the recipe uses a versioned archive).

```bash
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `git`, `nodejs`, `npm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[novahot]] · [[BlackArch Tools]]

