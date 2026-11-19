---
tool: "nox-framework"
build-system: "python"
upstream: "https://github.com/nox-project/nox-framework"
recipe: "blackarch"
source: "https://github.com/nox-project/nox-framework"
license: "Apache-2.0"
blackarch-pkgver: "1.0.5"
---

# Installing nox-framework

> OSINT & CTI Framework with 120+ sources, async performance, identity pivoting, and automated risk analysis.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nox-framework) (version `1.0.5`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S nox-framework    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nox-project/nox-framework) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `aiohttp`, `aiohttp-socks`, `aiosqlite`, `beautifulsoup4`, `build`, `certifi`, `cloudscraper`, `colorama`, `dnspython`, `fpdf2`, `httpx`, `installer`, `lxml`, `phonenumbers`, `pydantic`, `pydantic-core`, `requests`, `rich`, `setuptools`, `stem`, `wheel`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python -m build --wheel --no-isolation

  python build_sources.py
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[nox-framework]] · [[BlackArch Tools]]

