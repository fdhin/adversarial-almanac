---
tool: "dnsdiag"
build-system: "python"
upstream: "https://dnsdiag.org/"
recipe: "blackarch"
nix-package: "dnsdiag"
aur-package: "dnsdiag"
source: "https://dnsdiag.org/"
license: "unknown"
blackarch-pkgver: "v2.10.0.r2.g7bc7ac7"
---

# Installing dnsdiag

> DNS Diagnostics and Performance Measurement Tools.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnsdiag) (version `v2.10.0.r2.g7bc7ac7`, license n/a).

## Arch / BlackArch

```bash
sudo pacman -S dnsdiag    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://dnsdiag.org/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `aioquic`, `build`, `cryptography`, `cymruwhois`, `dnspython`, `h2`, `httpx`, `pip`, `typing`
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

Backlinks: [[networking]] · [[dnsdiag]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dnsdiag    # ad-hoc run, no install
nix profile install nixpkgs#dnsdiag   # or persist it
```
