---
tool: "ghunt"
build-system: "python"
upstream: "https://github.com/mxrch/GHunt"
recipe: "blackarch"
nix-package: "ghunt"
source: "https://github.com/mxrch/GHunt"
license: "AGPL-3.0-only"
blackarch-pkgver: "v2.2.0.r30.g5ee8939"
---

# Installing ghunt

> An offensive OSINT Google framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ghunt) (version `v2.2.0.r30.g5ee8939`, license AGPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S ghunt    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mxrch/GHunt) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `alive-progress`, `autoslot`, `beautifulsoup4`, `beautifultable`, `build`, `dateutil`, `dnspython`, `geopy`, `h2`, `httpx`, `humanize`, `imagehash`, `inflection`, `jsonpickle`, `packaging`, `pillow`, `pip`, `protobuf`, `rich`, `rich-argparse`
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

Backlinks: [[recon]] · [[ghunt]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ghunt    # ad-hoc run, no install
nix profile install nixpkgs#ghunt   # or persist it
```
