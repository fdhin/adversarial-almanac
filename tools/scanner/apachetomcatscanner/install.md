---
tool: "apachetomcatscanner"
build-system: "python"
upstream: "https://github.com/p0dalirius/ApacheTomcatScanner"
recipe: "blackarch"
nix-package: "apachetomcatscanner"
source: "https://github.com/p0dalirius/ApacheTomcatScanner"
license: "GPL2"
blackarch-pkgver: "3.2"
provides: ["ApacheTomcatScanner"]
---

# Installing apachetomcatscanner

> Apache Tomcat vulnerability scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/apachetomcatscanner) (version `3.2`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S apachetomcatscanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/p0dalirius/ApacheTomcatScanner) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `sectools`, `setuptools`, `xlsxwriter`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- Commands: `/usr/bin/ApacheTomcatScanner`

Backlinks: [[scanner]] · [[apachetomcatscanner]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#apachetomcatscanner    # ad-hoc run, no install
nix profile install nixpkgs#apachetomcatscanner   # or persist it
```
