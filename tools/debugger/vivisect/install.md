---
tool: "vivisect"
build-system: "python"
upstream: "https://github.com/vivisect/vivisect"
recipe: "blackarch"
nix-package: "vivisect"
source: "https://github.com/vivisect/vivisect"
license: "BSD"
blackarch-pkgver: "v1.3.2.r10.g66c93f0c"
---

# Installing vivisect

> A Python based static analysis and reverse engineering framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vivisect) (version `v1.3.2.r10.g66c93f0c`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S vivisect    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/vivisect/vivisect) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `cxxfilt`, `msgpack`, `pip`, `pyasn1`, `pyasn1-modules`, `pycparser`, `pyqt5`, `pyqt5-webengine`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[debugger]] · [[vivisect]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#vivisect    # ad-hoc run, no install
nix profile install nixpkgs#vivisect   # or persist it
```
