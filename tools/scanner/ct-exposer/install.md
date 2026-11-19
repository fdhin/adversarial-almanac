---
tool: "ct-exposer"
build-system: "scripts"
upstream: "https://github.com/chris408/ct-exposer"
recipe: "blackarch"
source: "https://github.com/chris408/ct-exposer"
license: "GPL3"
blackarch-pkgver: "24.71252ac"
---

# Installing ct-exposer

> An OSINT tool that discovers sub-domains by searching Certificate Transparency logs.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ct-exposer) (version `24.71252ac`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S ct-exposer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/chris408/ct-exposer) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `gevent`, `greenlet`, `requests`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[ct-exposer]] · [[BlackArch Tools]]

