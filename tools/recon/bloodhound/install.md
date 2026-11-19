---
tool: "bloodhound"
build-system: "node"
upstream: "https://github.com/SpecterOps/BloodHound-Legacy"
recipe: "blackarch"
aur-package: "bloodhound"
source: "https://github.com/SpecterOps/BloodHound-Legacy"
license: "GPL-3.0-only"
blackarch-pkgver: "v4.3.1.r61.ge8eda86"
---

# Installing bloodhound

> Six Degrees of Domain Admin

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bloodhound) (version `v4.3.1.r61.ge8eda86`, license GPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S bloodhound    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `nodejs` + `npm`):**

**Source:** download the release from the [upstream site](https://github.com/SpecterOps/BloodHound-Legacy) (the recipe uses a versioned archive).

```bash
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `git`, `nodejs`, `npm`
- **System libraries** (Arch name — translate for your distro):
  - `jre17-openjdk-headless`
  - `neo4j-community`
  - `nodejs-electron-packager`
  - `unzip`

## Exact build steps from the recipe

```bash
cd $pkgname

  npm install
  NODE_OPTIONS=--openssl-legacy-provider npm run build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[bloodhound]] · [[BlackArch Tools]]

