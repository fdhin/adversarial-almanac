---
tool: "starttls-mitm"
build-system: "scripts"
upstream: "https://github.com/ipopov/starttls-mitm"
recipe: "blackarch"
source: "https://github.com/ipopov/starttls-mitm"
license: "APACHE"
blackarch-pkgver: "7.b257756"
provides: ["mitm_listener"]
---

# Installing starttls-mitm

> A mitm proxy that will transparently proxy and dump both plaintext and TLS traffic.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/starttls-mitm) (version `7.b257756`, license APACHE).

## Arch / BlackArch

```bash
sudo pacman -S starttls-mitm    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ipopov/starttls-mitm) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- Commands: `/usr/bin/mitm_listener`

Backlinks: [[proxy]] · [[starttls-mitm]] · [[BlackArch Tools]]

