---
tool: "spraycharles"
build-system: "python"
upstream: "https://github.com/Tw1sm/spraycharles"
recipe: "blackarch"
nix-package: "spraycharles"
source: "https://github.com/Tw1sm/spraycharles"
license: "BSD-3-Clause"
blackarch-pkgver: "v2.0.2.r3.g131f8c6"
---

# Installing spraycharles

> Low and slow password spraying tool, designed to spray on an interval over a long period of time.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/spraycharles) (version `v2.0.2.r3.g131f8c6`, license BSD-3-Clause).

## Arch / BlackArch

```bash
sudo pacman -S spraycharles    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Tw1sm/spraycharles) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `click-config-file`, `discord-webhook`, `notifiers`, `numpy`, `pip`, `pymsteams`, `requests`, `requests-ntlm`, `rich`, `texttable`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[spraycharles]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#spraycharles    # ad-hoc run, no install
nix profile install nixpkgs#spraycharles   # or persist it
```
