---
tool: "hasher"
build-system: "scripts"
upstream: "https://github.com/ChrisTruncer/Hasher"
recipe: "blackarch"
source: "https://github.com/ChrisTruncer/Hasher"
license: "GPL3"
blackarch-pkgver: "48.40173c5"
---

# Installing hasher

> A tool that allows you to quickly hash plaintext strings, or compare hashed values with a plaintext locally.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hasher) (version `48.40173c5`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S hasher    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/ChrisTruncer/Hasher.git
cd Hasher
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-bcrypt, python2-passlib, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[hasher]] · [[BlackArch Tools]]

