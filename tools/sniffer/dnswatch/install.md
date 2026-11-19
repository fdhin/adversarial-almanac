---
tool: "dnswatch"
build-system: "python"
upstream: "https://github.com/HalilDeniz/DNSWatch"
recipe: "blackarch"
source: "https://github.com/HalilDeniz/DNSWatch"
license: "MIT"
blackarch-pkgver: "dnswatch.1.r18.g7ee12c9"
---

# Installing dnswatch

> DNS Traffic Sniffer and Analyzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnswatch) (version `dnswatch.1.r18.g7ee12c9`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S dnswatch    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/HalilDeniz/DNSWatch) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `pyfiglet`, `requests`, `scapy`, `setuptools`
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

Backlinks: [[sniffer]] · [[dnswatch]] · [[BlackArch Tools]]

