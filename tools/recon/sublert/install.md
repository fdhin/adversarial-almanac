---
tool: "sublert"
build-system: "python"
upstream: "https://github.com/yassineaboukir/sublert"
recipe: "blackarch"
source: "https://github.com/yassineaboukir/sublert"
license: "MIT"
blackarch-pkgver: "67.56d2a12"
---

# Installing sublert

> A security and reconnaissance tool which leverages certificate transparency to automatically monitor new subdomains deployed by specific organizations and issued TLS/SSL certificate.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sublert) (version `67.56d2a12`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S sublert    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/yassineaboukir/sublert) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `dnspython`, `psycopg2`, `requests`, `setuptools`, `termcolor`, `tld`
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

Backlinks: [[recon]] · [[sublert]] · [[BlackArch Tools]]

