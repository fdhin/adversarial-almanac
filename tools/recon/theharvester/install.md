---
tool: "theharvester"
build-system: "python"
upstream: "https://github.com/laramies/theHarvester"
recipe: "blackarch"
nix-package: "theharvester"
source: "https://github.com/laramies/theHarvester"
license: "GPL-2.0-or-later"
blackarch-pkgver: "4.11.1.r477.g6790b14"
provides: ["theHarvester"]
kali-package: "theharvester"
kali-version: "4.11.1-0kali2"
---

# Installing theharvester

> E-mails, subdomains and names Harvester - OSINT

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/theharvester) (version `4.11.1.r477.g6790b14`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S theharvester    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install theharvester    # Kali package version 4.11.1-0kali2
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/laramies/theHarvester.git
cd theHarvester
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `aiodns`, `aiofiles`, `aiohttp`, `aiohttp-socks`, `aiomultiprocess`, `aiosqlite`, `beautifulsoup4`, `build`, `censys`, `certifi`, `dateutil`, `dnspython`, `fastapi`, `gevent`, `grequests`, `lxml`, `mypy_extensions`, `netaddr`, `orjson`, `pip`, `playwright`, `plotly`, `pyppeteer`, `pytest`, `requests`, `retrying`, `shodan`, `slowapi`, `starlette`, `texttable`, `typing_extensions`, `ujson`, `uvloop`, `yaml`, `zope-event`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `flake8`
  - `python`
  - `uvicorn`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- Commands: `/usr/bin/theHarvester`

Backlinks: [[recon]] · [[theharvester]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#theharvester    # ad-hoc run, no install
nix profile install nixpkgs#theharvester   # or persist it
```
