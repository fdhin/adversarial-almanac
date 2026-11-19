---
tool: "cloudflare-enum"
build-system: "scripts"
upstream: "https://github.com/mandatoryprogrammer/cloudflare_enum"
recipe: "blackarch"
source: "https://github.com/mandatoryprogrammer/cloudflare_enum"
license: "custom:unknown"
blackarch-pkgver: "10.412387f"
---

# Installing cloudflare-enum

> Cloudflare DNS Enumeration Tool for Pentesters.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cloudflare-enum) (version `10.412387f`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S cloudflare-enum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/mandatoryprogrammer/cloudflare_enum.git
cd cloudflare_enum
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-requests, python2-simplejson (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[cloudflare-enum]] · [[BlackArch Tools]]

