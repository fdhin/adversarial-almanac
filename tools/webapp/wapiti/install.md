---
tool: "wapiti"
build-system: "python"
upstream: "https://github.com/wapiti-scanner/wapiti"
recipe: "blackarch"
nix-package: "wapiti"
aur-package: "wapiti"
source: "https://github.com/wapiti-scanner/wapiti"
license: "GPL-2.0-only"
blackarch-pkgver: "3.3.2.r10.g1e81698"
kali-package: "wapiti"
kali-version: "3.2.10+dfsg-4"
---

# Installing wapiti

> A vulnerability scanner for web applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wapiti) (version `3.3.2.r10.g1e81698`, license GPL-2.0-only).

## Arch / BlackArch

```bash
sudo pacman -S wapiti    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install wapiti    # Kali package version 3.2.10+dfsg-4
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/wapiti-scanner/wapiti) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `aiocache`, `aiosqlite`, `arsenic`, `async-timeout`, `beautifulsoup4`, `brotli`, `brotlicffi`, `browser-cookie3`, `cryptography`, `dnspython`, `h11`, `httpcore`, `httpx`, `httpx-ntlm`, `httpx-socks`, `humanize`, `importlib-metadata`, `keyring`, `loguru`, `mako`, `markupsafe`, `pip`, `prance`, `pyasn1`, `pysocks`, `pyspnego`, `setuptools`, `six`, `socksio`, `sqlalchemy`, `tld`, `wapiti-arsenic`, `wapiti-swagger`, `wheel`, `yaswfp`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `mitmproxy`
  - `python`
  - `sslyze`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[wapiti]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#wapiti    # ad-hoc run, no install
nix profile install nixpkgs#wapiti   # or persist it
```
