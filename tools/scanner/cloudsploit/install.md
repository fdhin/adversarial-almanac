---
tool: "cloudsploit"
build-system: "node"
upstream: "https://github.com/cloudsploit/scans"
recipe: "blackarch"
source: "https://github.com/cloudsploit/scans"
license: "GPL3"
blackarch-pkgver: "6744.cf9cd42d1"
---

# Installing cloudsploit

> AWS security scanning checks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cloudsploit) (version `6744.cf9cd42d1`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S cloudsploit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `nodejs` + `npm`):**

**Source:** download the release from the [upstream site](https://github.com/cloudsploit/scans) (the recipe uses a versioned archive).

```bash
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `git`, `nodejs`, `npm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[cloudsploit]] · [[BlackArch Tools]]

