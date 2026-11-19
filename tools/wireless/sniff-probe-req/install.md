---
tool: "sniff-probe-req"
build-system: "python"
upstream: "https://github.com/SkypLabs/sniff-probe-req"
recipe: "blackarch"
source: "https://github.com/SkypLabs/sniff-probe-req"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v0.8.0.r29.g0dacf88"
---

# Installing sniff-probe-req

> Wi-Fi Probe Requests Sniffer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sniff-probe-req) (version `v0.8.0.r29.g0dacf88`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S sniff-probe-req    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/SkypLabs/sniff-probe-req) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `netaddr`, `pip`, `scapy`, `wheel`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[sniff-probe-req]] · [[BlackArch Tools]]

