---
tool: "backdoor-factory"
build-system: "scripts"
upstream: "https://github.com/secretsquirrel/the-backdoor-factory"
recipe: "blackarch"
source: "https://github.com/secretsquirrel/the-backdoor-factory"
license: "BSD"
blackarch-pkgver: "210.0c53045"
---

# Installing backdoor-factory

> Patch win32/64 binaries with shellcode.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/backdoor-factory) (version `210.0c53045`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S backdoor-factory    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/secretsquirrel/the-backdoor-factory.git
cd the-backdoor-factory
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-capstone, python2-pefile, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[backdoor-factory]] · [[BlackArch Tools]]

