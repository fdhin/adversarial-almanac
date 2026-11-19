---
tool: "crackle"
build-system: "make"
upstream: "https://github.com/mikeryan/crackle/"
recipe: "blackarch"
nix-package: "crackle"
source: "https://github.com/mikeryan/crackle/"
license: "GPL"
blackarch-pkgver: "111.d83b4b6"
kali-package: "crackle"
kali-version: "0.1~git01282014-0kali4"
---

# Installing crackle

> Crack and decrypt BLE encryption.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/crackle) (version `111.d83b4b6`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S crackle    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install crackle    # Kali package version 0.1~git01282014-0kali4
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/mikeryan/crackle/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  CPPFLAGS=${CPPFLAGS/-D_FORTIFY_SOURCE=2/}

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[crackle]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#crackle    # ad-hoc run, no install
nix profile install nixpkgs#crackle   # or persist it
```
