---
tool: "kube-hunter"
build-system: "python"
upstream: "https://github.com/aquasecurity/kube-hunter"
recipe: "blackarch"
nix-package: "kube-hunter"
source: "https://github.com/aquasecurity/kube-hunter"
license: "Apache"
blackarch-pkgver: "703.bc47f08"
---

# Installing kube-hunter

> Hunt for security weaknesses in Kubernetes clusters.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kube-hunter) (version `703.bc47f08`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S kube-hunter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/aquasecurity/kube-hunter) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `future`, `kubernetes`, `netaddr`, `netifaces`, `packaging`, `pip`, `pluggy`, `prettytable`, `requests`, `ruamel-yaml`, `scapy`, `setuptools`, `urllib3`
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

Backlinks: [[scanner]] · [[kube-hunter]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#kube-hunter    # ad-hoc run, no install
nix profile install nixpkgs#kube-hunter   # or persist it
```
