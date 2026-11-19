---
tool: "hashpump"
build-system: "make"
upstream: "https://github.com/bwall/HashPump"
recipe: "blackarch"
nix-package: "hashpump"
source: "https://github.com/bwall/HashPump"
license: "MIT"
blackarch-pkgver: "49.314268e"
---

# Installing hashpump

> A tool to exploit the hash length extension attack in various hashing algorithms.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hashpump) (version `49.314268e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S hashpump    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/bwall/HashPump.git
cd HashPump
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[hashpump]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#hashpump    # ad-hoc run, no install
nix profile install nixpkgs#hashpump   # or persist it
```
