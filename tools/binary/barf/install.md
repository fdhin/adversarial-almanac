---
tool: "barf"
build-system: "python"
upstream: "https://github.com/programa-stic/barf-project"
recipe: "blackarch"
source: "https://github.com/programa-stic/barf-project"
license: "BSD"
blackarch-pkgver: "923.9547ef8"
---

# Installing barf

> A multiplatform open source Binary Analysis and Reverse engineering Framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/barf) (version `923.9547ef8`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S barf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/programa-stic/barf-project.git
cd barf-project
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `capstone`, `future`, `networkx`, `pefile`, `pydot`, `pyelftools`, `pygments`, `pyparsing`, `ropper`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `ropgadget`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[barf]] · [[BlackArch Tools]]

