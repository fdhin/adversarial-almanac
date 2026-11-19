---
tool: "above"
build-system: "python"
upstream: "https://github.com/casterbyte/Above"
recipe: "blackarch"
nix-package: "above"
source: "https://github.com/casterbyte/Above"
license: "Apache-2.0"
blackarch-pkgver: "v2.8.1.r0.g313f550"
kali-package: "above"
kali-version: "2.8.1-0kali2"
---

# Installing above

> Network Protocols Sniffer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/above) (version `v2.8.1.r0.g313f550`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S above    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install above    # Kali package version 2.8.1-0kali2
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/casterbyte/Above.git
cd Above
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `colorama`, `installer`, `setuptools`, `wheel`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `scapy`

## Exact build steps from the recipe

```bash
cd "$pkgname"

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[above]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#above    # ad-hoc run, no install
nix profile install nixpkgs#above   # or persist it
```
