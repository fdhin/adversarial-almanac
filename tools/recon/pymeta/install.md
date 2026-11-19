---
tool: "pymeta"
build-system: "python"
upstream: "https://github.com/m8r0wn/pymeta"
recipe: "blackarch"
source: "https://github.com/m8r0wn/pymeta"
license: "GPL-3.0-or-later"
blackarch-pkgver: "1.0.4.r25.gdb8a80b"
---

# Installing pymeta

> Auto Scanning to SSL Vulnerability.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pymeta) (version `1.0.4.r25.gdb8a80b`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S pymeta    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/m8r0wn/pymeta) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `build`, `installer`, `requests`, `setuptools`, `urllib3`, `wheel`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl-image-exiftool`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[pymeta]] · [[BlackArch Tools]]

