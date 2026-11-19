---
tool: "undbx"
build-system: "make"
upstream: "https://github.com/ZungBang/undbx"
recipe: "blackarch"
nix-package: "undbx"
source: "https://github.com/ZungBang/undbx"
license: "GPL"
blackarch-pkgver: "0.21.r3.g5e31c75"
kali-package: "undbx"
kali-version: "0.21-4"
---

# Installing undbx

> Extract e-mail messages from Outlook Express DBX files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/undbx) (version `0.21.r3.g5e31c75`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S undbx    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install undbx    # Kali package version 0.21-4
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/ZungBang/undbx) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd "$pkgname"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[undbx]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#undbx    # ad-hoc run, no install
nix profile install nixpkgs#undbx   # or persist it
```
