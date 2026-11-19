---
tool: "sslscan"
build-system: "make"
upstream: "https://github.com/rbsec/sslscan"
recipe: "arch-official/extra"
nix-package: "sslscan"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/sslscan/-/raw/main/PKGBUILD"
version: "2.2.3"
license: "unknown"
---

# Installing sslscan

> Tests SSL/TLS enabled services to discover supported cipher suites

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/sslscan/-/raw/main/PKGBUILD) (version `2.2.3`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S sslscan
```

## Kali / Debian-based — same package name

```bash
sudo apt install sslscan
```

## Nix (works on any distro)

```bash
nix run nixpkgs#sslscan    # ad-hoc run, no install
nix profile install nixpkgs#sslscan   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/rbsec/sslscan.git
cd sslscan
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"
  # OpenSSL 4 removed TLS methods and ASN.1 fields used by sslscan.
  # https://github.com/rbsec/sslscan/issues/361
  CFLAGS="$CFLAGS -I/usr/include/openssl-3" \
    LDFLAGS="$LDFLAGS -L/usr/lib/openssl-3" make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[recon]] · [[sslscan]] · [[Kali Gap Analysis]]
