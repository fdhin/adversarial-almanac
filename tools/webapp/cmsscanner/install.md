---
tool: "cmsscanner"
build-system: "scripts"
upstream: "https://github.com/wpscanteam/CMSScanner"
recipe: "blackarch"
aur-package: "cmsscanner"
source: "https://github.com/wpscanteam/CMSScanner"
license: "custom:unknown"
blackarch-pkgver: "0.15.0.r1.g65de34e"
---

# Installing cmsscanner

> CMS Scanner Framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cmsscanner) (version `0.15.0.r1.g65de34e`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S cmsscanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/wpscanteam/CMSScanner.git
cd CMSScanner
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `ruby`
  - `ruby-bundler`
  - `ruby-nokogiri`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[cmsscanner]] · [[BlackArch Tools]]

