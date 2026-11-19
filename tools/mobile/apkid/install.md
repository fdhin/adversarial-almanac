---
tool: "apkid"
build-system: "python"
upstream: "https://github.com/rednaga/APKiD"
recipe: "blackarch"
nix-package: "apkid"
source: "https://github.com/rednaga/APKiD"
license: "GPL-3.0-or-later, custom:unknown"
blackarch-pkgver: "3.0.0"
---

# Installing apkid

> Android Application Identifier for Packers, Protectors, Obfuscators and Oddities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/apkid) (version `3.0.0`, license GPL-3.0-or-later, custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S apkid    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/rednaga/APKiD) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `installer`, `setuptools`, `wheel`, `yara-python-dex`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[apkid]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#apkid    # ad-hoc run, no install
nix profile install nixpkgs#apkid   # or persist it
```
