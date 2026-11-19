---
tool: "tlspretense"
build-system: "scripts"
upstream: "https://github.com/iSECPartners/tlspretense"
recipe: "blackarch"
source: "https://github.com/iSECPartners/tlspretense"
license: "MIT"
blackarch-pkgver: "v0.6.2.r22.g0a5faf4"
---

# Installing tlspretense

> SSL/TLS client testing framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tlspretense) (version `v0.6.2.r22.g0a5faf4`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S tlspretense    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/iSECPartners/tlspretense) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `ruby`
  - `ruby-bundler`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[tlspretense]] · [[BlackArch Tools]]

