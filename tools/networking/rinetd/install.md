---
tool: "rinetd"
build-system: "other"
upstream: "http://www.boutell.com/rinetd"
recipe: "blackarch"
nix-package: "rinetd"
aur-package: "rinetd"
source: "http://www.boutell.com/rinetd"
license: "GPL"
blackarch-pkgver: "0.62"
---

# Installing rinetd

> Internet redirection server.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rinetd) (version `0.62`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S rinetd    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.boutell.com/rinetd) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
cd $pkgname

  cc -DLINUX -g   -c -o "$pkgname.o" "$pkgname.c"
  cc -DLINUX -g   -c -o match.o match.c
  gcc "$pkgname.o" match.o -o $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[networking]] · [[rinetd]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#rinetd    # ad-hoc run, no install
nix profile install nixpkgs#rinetd   # or persist it
```
