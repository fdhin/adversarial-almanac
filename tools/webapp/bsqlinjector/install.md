---
tool: "bsqlinjector"
build-system: "scripts"
upstream: "https://github.com/enjoiz/BSQLinjector"
recipe: "blackarch"
source: "https://github.com/enjoiz/BSQLinjector"
license: "custom:unknown"
blackarch-pkgver: "14.98c8ce8"
---

# Installing bsqlinjector

> Blind SQL injection exploitation tool written in ruby.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bsqlinjector) (version `14.98c8ce8`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S bsqlinjector    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/enjoiz/BSQLinjector.git
cd BSQLinjector
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[bsqlinjector]] · [[BlackArch Tools]]

