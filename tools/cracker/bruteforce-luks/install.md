---
tool: "bruteforce-luks"
build-system: "autotools"
upstream: "https://github.com/glv2/bruteforce-luks"
recipe: "blackarch"
nix-package: "bruteforce-luks"
aur-package: "bruteforce-luks"
source: "https://github.com/glv2/bruteforce-luks"
license: "GPL3"
blackarch-pkgver: "54.788d637"
---

# Installing bruteforce-luks

> Try to find the password of a LUKS encrypted volume.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bruteforce-luks) (version `54.788d637`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S bruteforce-luks    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/glv2/bruteforce-luks) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `cryptsetup`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./autogen.sh

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[cracker]] · [[bruteforce-luks]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#bruteforce-luks    # ad-hoc run, no install
nix profile install nixpkgs#bruteforce-luks   # or persist it
```
