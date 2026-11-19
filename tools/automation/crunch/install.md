---
tool: "crunch"
build-system: "make"
upstream: "http://sourceforge.net/projects/crunch-wordlist/"
recipe: "blackarch"
nix-package: "crunch"
aur-package: "crunch"
source: "http://sourceforge.net/projects/crunch-wordlist/"
license: "GPL"
blackarch-pkgver: "3.6"
kali-package: "crunch"
kali-version: "3.6-3.1"
---

# Installing crunch

> A wordlist generator for all combinations/permutations of a given character set.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/crunch) (version `3.6`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S crunch    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install crunch    # Kali package version 3.6-3.1
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/crunch-wordlist/) (the recipe uses a versioned archive).

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
- Data: `/usr/share/man`

Backlinks: [[automation]] · [[crunch]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#crunch    # ad-hoc run, no install
nix profile install nixpkgs#crunch   # or persist it
```
