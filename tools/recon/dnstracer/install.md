---
tool: "dnstracer"
build-system: "make"
upstream: "https://www.mavetju.org/unix/dnstracer.php"
recipe: "blackarch"
nix-package: "dnstracer"
aur-package: "dnstracer"
source: "https://www.mavetju.org/unix/dnstracer.php"
license: "custom:BSD"
blackarch-pkgver: "1.10"
kali-package: "dnstracer"
kali-version: "1.10-1"
---

# Installing dnstracer

> Determines where a given DNS server gets its information from, and follows the chain of DNS servers

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnstracer) (version `1.10`, license custom:BSD).

## Arch / BlackArch

```bash
sudo pacman -S dnstracer    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install dnstracer    # Kali package version 1.10-1
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://www.mavetju.org/unix/dnstracer.php) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd $pkgname

  make PREFIX=/usr CC="gcc ${CFLAGS} ${LDFLAGS}"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[recon]] · [[dnstracer]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dnstracer    # ad-hoc run, no install
nix profile install nixpkgs#dnstracer   # or persist it
```
