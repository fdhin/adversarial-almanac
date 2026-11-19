---
tool: "gdbgui"
build-system: "python"
upstream: "https://github.com/cs01/gdbgui"
recipe: "blackarch"
nix-package: "gdbgui"
source: "https://github.com/cs01/gdbgui"
license: "GPL3"
blackarch-pkgver: "438.773b916"
---

# Installing gdbgui

> Browser-based gdb frontend using Flask and JavaScript to visually debug C, C++, Go, or Rust.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gdbgui) (version `438.773b916`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S gdbgui    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/cs01/gdbgui) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `brotli`, `enum-compat`, `eventlet`, `flask`, `flask-compress`, `flask-socketio`, `gevent`, `pygdbmi`, `pygments`, `pypugjs`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `gdb`
  - `python`
  - `yarn`

## Exact build steps from the recipe

```bash
cd $pkgname

  export NODE_OPTIONS=--openssl-legacy-provider
  yarn install
  yarn build

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[debugger]] · [[gdbgui]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#gdbgui    # ad-hoc run, no install
nix profile install nixpkgs#gdbgui   # or persist it
```
