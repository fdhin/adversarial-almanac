---
tool: "cmospwd"
build-system: "make"
upstream: "http://www.cgsecurity.org/wiki/CmosPwd"
recipe: "blackarch"
nix-package: "cmospwd"
aur-package: "cmospwd"
source: "http://www.cgsecurity.org/wiki/CmosPwd"
license: "GPL"
blackarch-pkgver: "5.1"
kali-package: "cmospwd"
kali-version: "5.0+dfsg-3kali5"
---

# Installing cmospwd

> Decrypts password stored in CMOS used to access BIOS setup.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cmospwd) (version `5.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S cmospwd    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install cmospwd    # Kali package version 5.0+dfsg-3kali5
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.cgsecurity.org/wiki/CmosPwd) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver/src"

  make -B
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[cmospwd]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#cmospwd    # ad-hoc run, no install
nix profile install nixpkgs#cmospwd   # or persist it
```
