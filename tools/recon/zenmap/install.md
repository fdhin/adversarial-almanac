---
tool: "zenmap"
build-system: "python"
upstream: "https://nmap.org/"
recipe: "arch-official/extra"
nix-package: "zenmap"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/zenmap/-/raw/main/PKGBUILD"
version: "7.993"
license: "unknown"
provides: ["nmapfe", "xnmap"]
---

# Installing zenmap

> The Network Mapper Front End

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/zenmap/-/raw/main/PKGBUILD) (version `7.993`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S zenmap
```

## Kali / Debian-based — same package name

```bash
sudo apt install zenmap
```

## Nix (works on any distro)

```bash
nix run nixpkgs#zenmap    # ad-hoc run, no install
nix profile install nixpkgs#zenmap   # or persist it
```

## Any other Linux distro

**B. From source:**

**Source:** download the release from the [upstream site](https://nmap.org/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Exact build steps from the recipe

```bash
cd $pkgbase-$pkgver
  ./configure \
    --prefix=/usr \
    --with-libpcap=/usr \
    --with-libpcre=/usr \
    --with-zlib=/usr \
    --with-libssh2=/usr \
    --with-liblua=/usr \
    --without-ndiff \
    --without-zenmap
  make

  python -m build --wheel --no-isolation ndiff

  python -m build --wheel --no-isolation zenmap
```

## Installed layout (per recipe)

- Commands: `/usr/bin/nmapfe`, `/usr/bin/xnmap`
- Data: `/usr/share/applications`, `/usr/share/icons`, `/usr/share/man`

Backlinks: [[recon]] · [[zenmap]] · [[Kali Gap Analysis]]
