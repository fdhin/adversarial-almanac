---
tool: "chankro"
build-system: "scripts"
upstream: "https://github.com/TarlogicSecurity/Chankro"
recipe: "blackarch"
source: "https://github.com/TarlogicSecurity/Chankro"
license: "GPL3"
blackarch-pkgver: "21.7b6e844"
---

# Installing chankro

> Tool that generates a PHP capable of run a custom binary (like a meterpreter) or a bash script (p.e. reverse shell) bypassing disable_functions & open_basedir).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/chankro) (version `21.7b6e844`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S chankro    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/TarlogicSecurity/Chankro.git
cd Chankro
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[chankro]] · [[BlackArch Tools]]

