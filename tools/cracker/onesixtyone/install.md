---
tool: "onesixtyone"
build-system: "make"
upstream: "http://labs.portcullis.co.uk/application/onesixtyone/"
recipe: "blackarch"
nix-package: "onesixtyone"
source: "http://labs.portcullis.co.uk/application/onesixtyone/"
license: "GPL"
blackarch-pkgver: "0.7"
kali-package: "onesixtyone"
kali-version: "0.3.4-1"
---

# Installing onesixtyone

> An SNMP scanner that sends multiple SNMP requests to multiple IP addresses

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/onesixtyone) (version `0.7`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S onesixtyone    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install onesixtyone    # Kali package version 0.3.4-1
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://labs.portcullis.co.uk/application/onesixtyone/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[onesixtyone]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#onesixtyone    # ad-hoc run, no install
nix profile install nixpkgs#onesixtyone   # or persist it
```
