---
tool: "dcfldd"
build-system: "autotools"
upstream: "https://github.com/resurrecting-open-source-projects/dcfldd/"
recipe: "blackarch"
nix-package: "dcfldd"
aur-package: "dcfldd"
source: "https://github.com/resurrecting-open-source-projects/dcfldd/"
license: "GPL"
blackarch-pkgver: "1.9.3"
kali-package: "dcfldd"
kali-version: "1.9.3-2"
---

# Installing dcfldd

> DCFL (DoD Computer Forensics Lab) dd replacement with hashing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dcfldd) (version `1.9.3`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S dcfldd    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install dcfldd    # Kali package version 1.9.3-2
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/resurrecting-open-source-projects/dcfldd/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr --mandir=/usr/share/man

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[dcfldd]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dcfldd    # ad-hoc run, no install
nix profile install nixpkgs#dcfldd   # or persist it
```
