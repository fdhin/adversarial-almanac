---
tool: "peach-fuzz"
build-system: "scripts"
upstream: "https://github.com/Caleb1994/peach"
recipe: "blackarch"
source: "https://github.com/Caleb1994/peach"
license: "custom:unknown"
blackarch-pkgver: "55.404e8ee"
---

# Installing peach-fuzz

> Simple vulnerability scanning framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/peach-fuzz) (version `55.404e8ee`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S peach-fuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Caleb1994/peach.git
cd peach
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pwntools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-colorama (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[peach-fuzz]] · [[BlackArch Tools]]

