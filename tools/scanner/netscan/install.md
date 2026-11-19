---
tool: "netscan"
build-system: "other"
upstream: "http://packetstormsecurity.com/files/125569/Netscan-Port-Scanner-1.0.html"
recipe: "blackarch"
nix-package: "netscan"
source: "https://dl.packetstormsecurity.net/UNIX/scanners/netscan.c"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing netscan

> Tcp/Udp/Tor port scanner with: synpacket, connect TCP/UDP and socks5 (tor connection).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netscan) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S netscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/125569/Netscan-Port-Scanner.0.html) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
gcc -lpthread "$pkgname.c" -o $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[netscan]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#netscan    # ad-hoc run, no install
nix profile install nixpkgs#netscan   # or persist it
```
