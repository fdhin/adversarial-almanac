---
tool: "binproxy"
build-system: "scripts"
upstream: "https://github.com/nccgroup/BinProxy/"
recipe: "blackarch"
source: "https://github.com/nccgroup/BinProxy/"
license: "AGPL-3.0-or-later"
blackarch-pkgver: "8.d02fce9"
---

# Installing binproxy

> A proxy for arbitrary TCP connections.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/binproxy) (version `8.d02fce9`, license AGPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S binproxy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/nccgroup/BinProxy.git
cd BinProxy
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

Backlinks: [[proxy]] · [[binproxy]] · [[BlackArch Tools]]

