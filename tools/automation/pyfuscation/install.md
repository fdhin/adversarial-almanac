---
tool: "pyfuscation"
build-system: "scripts"
upstream: "https://github.com/CBHue/PyFuscation"
recipe: "blackarch"
source: "https://github.com/CBHue/PyFuscation"
license: "BSD"
blackarch-pkgver: "17.6d8d53f"
---

# Installing pyfuscation

> Obfuscate powershell scripts by replacing Function names, Variables and Parameters.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pyfuscation) (version `17.6d8d53f`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S pyfuscation    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/CBHue/PyFuscation.git
cd PyFuscation
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[pyfuscation]] · [[BlackArch Tools]]

