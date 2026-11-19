---
tool: "waymore"
build-system: "python"
upstream: "https://github.com/xnl-h4ck3r/waymore"
recipe: "blackarch"
nix-package: "waymore"
aur-package: "waymore"
source: "https://github.com/xnl-h4ck3r/waymore"
license: "MIT"
blackarch-pkgver: "v8.9.r0.g4e02de5"
---

# Installing waymore

> Find way more from the Wayback Machine, Common Crawl, Alien Vault OTX, URLScan & VirusTotal.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/waymore) (version `v8.9.r0.g4e02de5`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S waymore    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/xnl-h4ck3r/waymore) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `psutil`, `pyaml`, `requests`, `setuptools`, `termcolor`, `tldextract`
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

Backlinks: [[recon]] · [[waymore]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#waymore    # ad-hoc run, no install
nix profile install nixpkgs#waymore   # or persist it
```
