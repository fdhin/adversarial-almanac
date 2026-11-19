---
tool: "dnsrecon"
build-system: "python"
upstream: "https://github.com/darkoperator/dnsrecon"
recipe: "blackarch"
nix-package: "dnsrecon"
source: "https://github.com/darkoperator/dnsrecon"
license: "custom"
blackarch-pkgver: "1.6.3"
kali-package: "dnsrecon"
kali-version: "1.6.3-1"
---

# Installing dnsrecon

> Python script for enumeration of hosts, subdomains and emails from a given domain using google.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnsrecon) (version `1.6.3`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S dnsrecon    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install dnsrecon    # Kali package version 1.6.3-1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/darkoperator/dnsrecon) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `dnspython`, `loguru`, `lxml`, `netaddr`, `pip`, `requests`
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

Backlinks: [[recon]] · [[dnsrecon]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dnsrecon    # ad-hoc run, no install
nix profile install nixpkgs#dnsrecon   # or persist it
```
