---
tool: "usnparser"
build-system: "python"
upstream: "https://pypi.org/project/usnparser/#files"
recipe: "blackarch"
source: "https://pypi.org/project/usnparser/#files"
license: "APACHE"
blackarch-pkgver: "4.1.5"
provides: ["usn", "usn.py"]
---

# Installing usnparser

> A Python script to parse the NTFS USN journal.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/usnparser) (version `4.1.5`, license APACHE).

## Arch / BlackArch

```bash
sudo pacman -S usnparser    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/usnparser/#files) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- Commands: `/usr/bin/usn`, `/usr/bin/usn.py`

Backlinks: [[forensic]] · [[usnparser]] · [[BlackArch Tools]]

