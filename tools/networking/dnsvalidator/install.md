---
tool: "dnsvalidator"
build-system: "python"
upstream: "https://github.com/vortexau/dnsvalidator"
recipe: "blackarch"
nix-package: "dnsvalidator"
source: "https://github.com/vortexau/dnsvalidator"
license: "custom:unknown"
blackarch-pkgver: "90.46f0f32"
---

# Installing dnsvalidator

> Maintains a list of IPv4 DNS servers by verifying them against baseline servers, and ensuring accurate responses.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnsvalidator) (version `90.46f0f32`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dnsvalidator    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/vortexau/dnsvalidator) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `colorclass`, `dnspython`, `netaddr`, `pip`, `requests`, `setuptools`, `wheel`
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

Backlinks: [[networking]] · [[dnsvalidator]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dnsvalidator    # ad-hoc run, no install
nix profile install nixpkgs#dnsvalidator   # or persist it
```
