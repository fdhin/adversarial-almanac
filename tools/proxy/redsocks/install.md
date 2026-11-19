---
tool: "redsocks"
build-system: "make"
upstream: "https://github.com/darkk/redsocks"
recipe: "blackarch"
nix-package: "redsocks"
aur-package: "redsocks"
source: "https://github.com/darkk/redsocks"
license: "Apache"
blackarch-pkgver: "211.19b822e"
kali-package: "redsocks"
kali-version: "0.5-2+b5"
---

# Installing redsocks

> Transparent redirector of any TCP connection to proxy.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/redsocks) (version `211.19b822e`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S redsocks    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install redsocks    # Kali package version 0.5-2+b5
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/darkk/redsocks) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libevent`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[proxy]] · [[redsocks]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#redsocks    # ad-hoc run, no install
nix profile install nixpkgs#redsocks   # or persist it
```
