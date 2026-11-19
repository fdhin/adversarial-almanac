---
tool: "hashrat"
build-system: "make"
upstream: "https://github.com/ColumPaget/Hashrat"
recipe: "blackarch"
nix-package: "hashrat"
aur-package: "hashrat"
source: "https://github.com/ColumPaget/Hashrat"
license: "GPL3"
blackarch-pkgver: "1.15"
kali-package: "hashrat"
kali-version: "1.25+ds-2"
---

# Installing hashrat

> Hashing tool supporting MD5, SHA1, SHA256, SHA512, Whirlpool, JH and their HMAC.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hashrat) (version `1.15`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S hashrat    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install hashrat    # Kali package version 1.25+ds-2
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/ColumPaget/Hashrat
cd Hashrat
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[hashrat]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#hashrat    # ad-hoc run, no install
nix profile install nixpkgs#hashrat   # or persist it
```
