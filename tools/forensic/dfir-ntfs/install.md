---
tool: "dfir-ntfs"
build-system: "python"
upstream: "https://github.com/msuhanov/dfir_ntfs"
recipe: "blackarch"
source: "https://github.com/msuhanov/dfir_ntfs"
license: "GPL-3.0-or-later, custom:unknown"
blackarch-pkgver: "1.1.20"
---

# Installing dfir-ntfs

> An NTFS parser for digital forensics & incident response.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dfir-ntfs) (version `1.1.20`, license GPL-3.0-or-later, custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dfir-ntfs    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/msuhanov/dfir_ntfs) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[dfir-ntfs]] · [[BlackArch Tools]]

