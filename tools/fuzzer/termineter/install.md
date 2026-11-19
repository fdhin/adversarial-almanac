---
tool: "termineter"
build-system: "python"
upstream: "https://code.google.com/p/termineter/"
recipe: "blackarch"
nix-package: "termineter"
source: "https://code.google.com/p/termineter/"
license: "BSD"
blackarch-pkgver: "210.a802f94"
kali-package: "termineter"
kali-version: "1.0.6-1"
---

# Installing termineter

> Smart meter testing framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/termineter) (version `210.a802f94`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S termineter    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install termineter    # Kali package version 1.0.6-1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/p/termineter/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `crcelk`, `pip`, `pluginbase`, `pyasn1`, `pyserial`, `setuptools`, `smoke-zephyr`, `tabulate`, `termcolor`, `wheel`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[termineter]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#termineter    # ad-hoc run, no install
nix profile install nixpkgs#termineter   # or persist it
```
