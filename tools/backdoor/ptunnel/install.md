---
tool: "ptunnel"
build-system: "make"
upstream: "https://www.cs.uit.no/~daniels/PingTunnel"
recipe: "arch-official/extra"
nix-package: "ptunnel"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/ptunnel/-/raw/main/PKGBUILD"
version: "0.72"
license: "BSD"
provides: ["ptunnel"]
---

# Installing ptunnel

> Tunnel TCP connections over ICMP packets

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/ptunnel/-/raw/main/PKGBUILD) (version `0.72`, license BSD).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S ptunnel
```

## Kali / Debian-based — same package name

```bash
sudo apt install ptunnel
```

## Nix (works on any distro)

```bash
nix run nixpkgs#ptunnel    # ad-hoc run, no install
nix profile install nixpkgs#ptunnel   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.mit.edu/afs.new/sipb/user/golem/tmp/ptunnel-0.61.orig/web/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$srcdir/PingTunnel"
  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/ptunnel`

Backlinks: [[backdoor]] · [[ptunnel]] · [[Kali Gap Analysis]]
