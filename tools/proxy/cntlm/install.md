---
tool: "cntlm"
build-system: "autotools"
upstream: "https://github.com/bseb/cntlm"
recipe: "blackarch"
nix-package: "cntlm"
aur-package: "cntlm"
source: "https://github.com/bseb/cntlm"
license: "GPL2"
blackarch-pkgver: "4.b35d55c"
---

# Installing cntlm

> An NTLM, NTLM2SR, and NTLMv2 authenticating HTTP proxy.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cntlm) (version `4.b35d55c`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S cntlm    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/bseb/cntlm) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd "$pkgname/$pkgname-0.92.3"

	./configure

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[proxy]] · [[cntlm]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#cntlm    # ad-hoc run, no install
nix profile install nixpkgs#cntlm   # or persist it
```
