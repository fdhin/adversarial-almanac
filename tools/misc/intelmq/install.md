---
tool: "intelmq"
build-system: "python"
upstream: "https://github.com/certtools/intelmq"
recipe: "blackarch"
source: "https://github.com/certtools/intelmq"
license: "AGPL3"
blackarch-pkgver: "3.5.0.r36.gbbe452a20"
---

# Installing intelmq

> A tool for collecting and processing security feeds using a message queuing protocol.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/intelmq) (version `3.5.0.r36.gbbe452a20`, license AGPL3).

## Arch / BlackArch

```bash
sudo pacman -S intelmq    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/certtools/intelmq) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `dateutil`, `dnspython`, `psutil`, `psycopg2`, `pytz`, `redis`, `requests`, `ruamel-yaml`, `setuptools`, `termstyle`
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

Backlinks: [[misc]] · [[intelmq]] · [[BlackArch Tools]]

