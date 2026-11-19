---
tool: "jstillery"
build-system: "node"
upstream: "https://github.com/mindedsecurity/JStillery"
recipe: "blackarch"
source: "https://github.com/mindedsecurity/JStillery"
license: "GPL3"
blackarch-pkgver: "65.512e9af"
---

# Installing jstillery

> Advanced JavaScript Deobfuscation via Partial Evaluation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jstillery) (version `65.512e9af`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S jstillery    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `nodejs` + `npm`):**

```bash
git clone https://github.com/mindedsecurity/JStillery.git
cd JStillery
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `git`, `nodejs`, `npm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[jstillery]] · [[BlackArch Tools]]

