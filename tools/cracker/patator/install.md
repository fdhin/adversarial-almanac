---
tool: "patator"
build-system: "python"
upstream: "https://github.com/lanjelot/patator"
recipe: "blackarch"
aur-package: "patator"
source: "https://github.com/lanjelot/patator"
license: "GPL-2.0-or-later"
blackarch-pkgver: "1.1.0.r4.g964e87c"
kali-package: "patator"
kali-version: "1.1.0-1"
---

# Installing patator

> A multi-purpose bruteforcer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/patator) (version `1.1.0.r4.g964e87c`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S patator    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install patator    # Kali package version 1.1.0-1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/lanjelot/patator) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `ajpy`, `build`, `cx_oracle`, `dnspython`, `ipy`, `paramiko`, `pip`, `psycopg2`, `pyasn1`, `pycryptodome`, `pycurl`, `pyopenssl`, `pysnmp`
- **Build tools**: `git`, `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `ike-scan`
  - `impacket`
  - `mysql-python`
  - `openldap`
  - `python`
  - `unzip`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[patator]] · [[BlackArch Tools]]

