---
tool: "braa"
build-system: "make"
upstream: "http://s-tech.elsat.net.pl/braa/"
recipe: "blackarch"
nix-package: "braa"
source: "http://s-tech.elsat.net.pl/braa/"
license: "GPL-1.0-only"
blackarch-pkgver: "0.82"
kali-package: "braa"
kali-version: "0.90.1-1"
---

# Installing braa

> A mass snmp scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/braa) (version `0.82`, license GPL-1.0-only).

## Arch / BlackArch

```bash
sudo pacman -S braa    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install braa    # Kali package version 0.90.1-1
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://s-tech.elsat.net.pl/braa/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `nasm`
  - `subversion`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make CFLAGS="$CFLAGS"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[braa]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#braa    # ad-hoc run, no install
nix profile install nixpkgs#braa   # or persist it
```
