---
tool: "dutas"
build-system: "scripts"
upstream: "https://github.com/dungtv543/Dutas"
recipe: "blackarch"
source: "https://github.com/dungtv543/Dutas"
license: "custom:unknown"
blackarch-pkgver: "10.37fa3ab"
---

# Installing dutas

> Analysis PE file or Shellcode.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dutas) (version `10.37fa3ab`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dutas    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/dungtv543/Dutas.git
cd Dutas
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `capstone`
  - `python2`
  - `unicorn`
- ⚠️ Python 2 only deps: python2-capstone, python2-pefile, python2-setuptools, python2-unicorn (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[dutas]] · [[BlackArch Tools]]

