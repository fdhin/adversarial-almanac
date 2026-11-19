---
tool: "anubis-netsec"
build-system: "python"
upstream: "https://github.com/jonluca/anubis"
recipe: "blackarch"
source: "https://github.com/jonluca/anubis"
license: "MIT"
blackarch-pkgver: "1.4.0.r0.g2f77f98"
---

# Installing anubis-netsec

> Subdomain enumeration and information gathering tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/anubis-netsec) (version `1.4.0.r0.g2f77f98`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S anubis-netsec    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/jonluca/anubis) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `censys`, `dnspython`, `docopt`, `pip`, `python-nmap`, `requests`, `shodan`
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

Backlinks: [[scanner]] · [[anubis-netsec]] · [[BlackArch Tools]]

