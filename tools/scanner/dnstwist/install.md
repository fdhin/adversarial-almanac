---
tool: "dnstwist"
build-system: "python"
upstream: "https://github.com/elceef/dnstwist"
recipe: "blackarch"
nix-package: "dnstwist"
aur-package: "dnstwist"
source: "https://github.com/elceef/dnstwist"
license: "custom:unknown"
blackarch-pkgver: "645.3413953"
---

# Installing dnstwist

> Domain name permutation engine for detecting typo squatting, phishing and corporate espionage.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnstwist) (version `645.3413953`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dnstwist    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/elceef/dnstwist) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `dnspython`, `geoip`, `ppdeep`, `python-whois`, `requests`, `setuptools`, `tld`
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

Backlinks: [[scanner]] · [[dnstwist]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dnstwist    # ad-hoc run, no install
nix profile install nixpkgs#dnstwist   # or persist it
```
