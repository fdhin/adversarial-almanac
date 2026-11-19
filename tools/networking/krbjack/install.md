---
tool: "krbjack"
build-system: "python"
upstream: "https://github.com/almandin/krbjack"
recipe: "blackarch"
nix-package: "krbjack"
source: "https://github.com/almandin/krbjack"
license: "custom:THE BEER-WARE LICENSE"
blackarch-pkgver: "1.2.0"
---

# Installing krbjack

> DNS dynamic update abuse in ADIDNS and MitM attack using Kerberos AP-REQ hijacking.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/krbjack) (version `1.2.0`, license custom:THE BEER-WARE LICENSE).

## Arch / BlackArch

```bash
sudo pacman -S krbjack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/almandin/krbjack) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `colorama`, `dnspython`, `pip`, `scapy`
- **System libraries** (Arch name — translate for your distro):
  - `impacket-ba`
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[krbjack]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#krbjack    # ad-hoc run, no install
nix profile install nixpkgs#krbjack   # or persist it
```
