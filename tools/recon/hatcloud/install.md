---
tool: "hatcloud"
build-system: "scripts"
upstream: "https://github.com/HatBashBR/HatCloud"
recipe: "blackarch"
source: "https://github.com/HatBashBR/HatCloud"
license: "GPL3"
blackarch-pkgver: "33.3012ad6"
---

# Installing hatcloud

> Bypass CloudFlare with Ruby.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hatcloud) (version `33.3012ad6`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S hatcloud    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/HatBashBR/HatCloud.git
cd HatCloud
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[hatcloud]] · [[BlackArch Tools]]

