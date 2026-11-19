---
tool: "wssip"
build-system: "node"
upstream: "https://github.com/nccgroup/wssip"
recipe: "blackarch"
source: "https://github.com/nccgroup/wssip"
license: "AGPL-3.0-or-later"
blackarch-pkgver: "1.1.0.r14.g56d0d2c"
---

# Installing wssip

> Application for capturing, modifying and sending custom WebSocket data from client to server and vice versa.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wssip) (version `1.1.0.r14.g56d0d2c`, license AGPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S wssip    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `nodejs` + `npm`):**

**Source:** download the release from the [upstream site](https://github.com/nccgroup/wssip) (the recipe uses a versioned archive).

```bash
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `git`, `nodejs`, `npm`
- **System libraries** (Arch name — translate for your distro):
  - `coffee-script`
  - `electron`
  - `libxtst`
  - `python`
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[wssip]] · [[BlackArch Tools]]

