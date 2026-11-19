---
tool: "raccoon"
build-system: "python"
upstream: "https://github.com/evyatarmeged/Raccoon"
recipe: "blackarch"
aur-package: "raccoon"
source: "https://github.com/evyatarmeged/Raccoon"
license: "MIT"
blackarch-pkgver: "190.44024ab"
---

# Installing raccoon

> A high performance offensive security tool for reconnaissance and vulnerability scanning.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/raccoon) (version `190.44024ab`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S raccoon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/evyatarmeged/Raccoon.git
cd Raccoon
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `click`, `dnspython`, `fake-useragent`, `lxml`, `pysocks`, `requests`, `setuptools`, `xmltodict`
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

Backlinks: [[recon]] · [[raccoon]] · [[BlackArch Tools]]

