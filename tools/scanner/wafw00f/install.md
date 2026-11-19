---
tool: "wafw00f"
build-system: "python"
upstream: "https://github.com/EnableSecurity/wafw00f"
recipe: "blackarch"
nix-package: "wafw00f"
aur-package: "wafw00f"
source: "https://github.com/EnableSecurity/wafw00f"
license: "GPL"
blackarch-pkgver: "913.69fbe39"
kali-package: "wafw00f"
kali-version: "2.4.2-1"
---

# Installing wafw00f

> Identify and fingerprint Web Application Firewall (WAF) products protecting a website.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wafw00f) (version `913.69fbe39`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S wafw00f    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install wafw00f    # Kali package version 2.4.2-1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/EnableSecurity/wafw00f) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `httpretty`, `pluginbase`, `requests`, `setuptools`, `urllib3`
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

Backlinks: [[scanner]] · [[wafw00f]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#wafw00f    # ad-hoc run, no install
nix profile install nixpkgs#wafw00f   # or persist it
```
