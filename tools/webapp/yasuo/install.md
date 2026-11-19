---
tool: "yasuo"
build-system: "scripts"
upstream: "https://github.com/0xsauby/yasuo"
recipe: "blackarch"
source: "https://github.com/0xsauby/yasuo"
license: "GPL3"
blackarch-pkgver: "121.994dcb1"
---

# Installing yasuo

> A ruby script that scans for vulnerable & exploitable 3rd-party web applications on a network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/yasuo) (version `121.994dcb1`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S yasuo    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/0xsauby/yasuo) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `ruby`
  - `ruby-bundler`
  - `ruby-pkg-config`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[yasuo]] · [[BlackArch Tools]]

