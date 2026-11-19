---
tool: "openwebrx"
build-system: "python"
upstream: "https://github.com/jketterl/openwebrx"
recipe: "blackarch"
nix-package: "openwebrx"
aur-package: "openwebrx"
source: "https://github.com/jketterl/openwebrx"
license: "AGPL-3.0-only"
blackarch-pkgver: "1.2.2.r389.g640c5b0"
---

# Installing openwebrx

> Open source, multi-user SDR receiver software with a web interface.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/openwebrx) (version `1.2.2.r389.g640c5b0`, license AGPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S openwebrx    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/jketterl/openwebrx) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `js8py`, `pycsdr`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `airspyhf`
  - `csdr`
  - `libsdrplay`
  - `netcat`
  - `owrx_connector`
  - `python`
  - `rtl-sdr`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[openwebrx]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#openwebrx    # ad-hoc run, no install
nix profile install nixpkgs#openwebrx   # or persist it
```
