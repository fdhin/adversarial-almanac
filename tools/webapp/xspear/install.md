---
tool: "xspear"
build-system: "scripts"
upstream: "https://github.com/hahwul/XSpear"
recipe: "blackarch"
source: "https://github.com/hahwul/XSpear"
license: "MIT"
blackarch-pkgver: "1.4.1.r10.gf7bab38"
---

# Installing xspear

> Powerful XSS Scanning and Parameter analysis tool&gem.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xspear) (version `1.4.1.r10.gf7bab38`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S xspear    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/hahwul/XSpear.git
cd XSpear
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

Backlinks: [[webapp]] · [[xspear]] · [[BlackArch Tools]]

