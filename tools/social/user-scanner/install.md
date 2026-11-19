---
tool: "user-scanner"
build-system: "python"
upstream: "https://github.com/kaifcodec/user-scanner"
recipe: "blackarch"
nix-package: "user-scanner"
aur-package: "user-scanner"
source: "https://github.com/kaifcodec/user-scanner"
license: "MIT"
blackarch-pkgver: "1.5.2"
---

# Installing user-scanner

> OSINT tool that analyzes username and email presence across multiple platforms, intended for security research, investigations, legitimate analysis.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/user-scanner) (version `1.5.2`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S user-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/kaifcodec/user-scanner) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `colorama`, `curl_cffi`, `flit-core`, `h2`, `httpx`, `installer`, `mcp`, `pillow`, `reportlab`, `rich`, `setuptools`, `socksio`, `svglib`, `wheel`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[user-scanner]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#user-scanner    # ad-hoc run, no install
nix profile install nixpkgs#user-scanner   # or persist it
```
