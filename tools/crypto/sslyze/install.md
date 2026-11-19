---
tool: "sslyze"
build-system: "python"
upstream: "https://github.com/nabla-c0d3/sslyze"
recipe: "blackarch"
aur-package: "sslyze"
source: "https://github.com/nabla-c0d3/sslyze"
license: "AGPL-3.0-or-later"
blackarch-pkgver: "6.3.1"
kali-package: "sslyze"
kali-version: "6.3.1-0kali4"
---

# Installing sslyze

> Python tool for analyzing the configuration of SSL servers and for identifying misconfigurations.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sslyze) (version `6.3.1`, license AGPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S sslyze    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install sslyze    # Kali package version 6.3.1-0kali4
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nabla-c0d3/sslyze) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `cryptography`, `installer`, `nassl`, `pydantic`, `pyopenssl`, `setuptools`, `tls-parser`, `typing_extensions`, `wheel`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[sslyze]] · [[BlackArch Tools]]

