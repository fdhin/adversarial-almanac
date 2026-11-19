---
tool: "hoper"
build-system: "scripts"
upstream: "https://github.com/gabamnml/hoper"
recipe: "blackarch"
source: "https://github.com/gabamnml/hoper"
license: "MIT"
blackarch-pkgver: "v0.1.1.r0.g8d5dbd9"
---

# Installing hoper

> Trace URL's jumps across the rel links to obtain the last URL.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hoper) (version `v0.1.1.r0.g8d5dbd9`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S hoper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/gabamnml/hoper) (the recipe uses a versioned archive).

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

Backlinks: [[recon]] · [[hoper]] · [[BlackArch Tools]]

