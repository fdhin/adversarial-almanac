---
tool: "pyexfil"
build-system: "python"
upstream: "https://github.com/ytisf/PyExfil"
recipe: "blackarch"
source: "https://github.com/ytisf/PyExfil"
license: "MIT"
blackarch-pkgver: "84.0297b46"
---

# Installing pyexfil

> A couple of beta stage tools for data exfiltration.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pyexfil) (version `84.0297b46`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pyexfil    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/ytisf/PyExfil.git
cd PyExfil
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `base58`, `faker`, `librosa`, `luhn`, `names`, `numpy`, `pillow`, `progressbar`, `pycryptodome`, `pycryptodomex`, `pytube3`, `qrcode`, `requests`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[pyexfil]] · [[BlackArch Tools]]

