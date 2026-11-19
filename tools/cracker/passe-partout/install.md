---
tool: "passe-partout"
build-system: "other"
upstream: "http://www.hsc.fr/ressources/outils/passe-partout/index.html.en"
recipe: "blackarch"
source: "http://www.hsc.fr/ressources/outils/passe-partout/index.html.en"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing passe-partout

> Tool to extract RSA and DSA private keys from any process linked with OpenSSL. The target memory is scanned to lookup specific OpenSSL patterns.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/passe-partout) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S passe-partout    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.hsc.fr/ressources/outils/passe-partout/index.html.en) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./build.sh
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[passe-partout]] · [[BlackArch Tools]]

