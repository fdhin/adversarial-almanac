---
tool: "bluebox-ng"
build-system: "node"
upstream: "https://github.com/jesusprubio/bluebox-ng"
recipe: "blackarch"
source: "https://github.com/jesusprubio/bluebox-ng"
license: "GPL-3.0-only"
blackarch-pkgver: "1.1.0"
---

# Installing bluebox-ng

> A GPL VoIP/UC vulnerability scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bluebox-ng) (version `1.1.0`, license GPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S bluebox-ng    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `nodejs` + `npm`):**

**Source:** download the release from the [upstream site](https://github.com/jesusprubio/bluebox-ng) (the recipe uses a versioned archive).

```bash
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `nodejs`
- **System libraries** (Arch name — translate for your distro):
  - `coffee-script`
  - `nmap`
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[voip]] · [[bluebox-ng]] · [[BlackArch Tools]]

