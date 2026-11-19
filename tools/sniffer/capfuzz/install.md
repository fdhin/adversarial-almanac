---
tool: "capfuzz"
build-system: "python"
upstream: "https://github.com/MobSF/CapFuzz"
recipe: "blackarch"
source: "https://github.com/MobSF/CapFuzz"
license: "GPL3"
blackarch-pkgver: "34.97ac312"
---

# Installing capfuzz

> Capture, fuzz and intercept web traffic.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/capfuzz) (version `34.97ac312`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S capfuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/MobSF/CapFuzz.git
cd CapFuzz
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `setuptools`, `tornado`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `mitmproxy`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[capfuzz]] · [[BlackArch Tools]]

