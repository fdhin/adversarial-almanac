---
tool: "maigret"
build-system: "python"
upstream: "https://github.com/soxoj/maigret"
recipe: "blackarch"
nix-package: "maigret"
aur-package: "maigret"
source: "https://github.com/soxoj/maigret"
license: "MIT"
blackarch-pkgver: "0.6.5"
provides: ["update_sitesmd"]
---

# Installing maigret

> OSINT username checker. Collect a dossier on a person by username from a huge number of sites.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/maigret) (version `0.6.5`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S maigret    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/soxoj/maigret) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `aiodns`, `aiohttp`, `aiohttp-socks`, `alive-progress`, `asgiref`, `build`, `certifi`, `colorama`, `curl_cffi`, `dateutil`, `flask`, `html5lib`, `jinja`, `lxml`, `markupsafe`, `networkx`, `pip`, `platformdirs`, `pycountry`, `pysocks`, `pyvis`, `reportlab`, `requests`, `socid-extractor`, `soupsieve`, `typing_extensions`, `xmind`, `yarl`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- Commands: `/usr/bin/update_sitesmd`

Backlinks: [[social]] · [[maigret]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#maigret    # ad-hoc run, no install
nix profile install nixpkgs#maigret   # or persist it
```
