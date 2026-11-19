---
tool: "ike-scan"
build-system: "autotools"
upstream: "http://www.nta-monitor.com/tools/ike-scan/"
recipe: "blackarch"
nix-package: "ike-scan"
aur-package: "ike-scan"
source: "http://www.nta-monitor.com/tools/ike-scan/"
license: "GPL"
blackarch-pkgver: "1.9.5"
kali-package: "ike-scan"
kali-version: "1.9.5-2"
---

# Installing ike-scan

> A tool that uses IKE protocol to discover, fingerprint and test IPSec VPN servers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ike-scan) (version `1.9.5`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S ike-scan    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install ike-scan    # Kali package version 1.9.5-2
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.nta-monitor.com/tools/ike-scan/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `gcc`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  if [[ "$CARCH" == "x86_64" ]]; then
    ./configure --prefix=/usr --mandir=/usr/share/man --with-openssl
  else
    ./configure --prefix=/usr --mandir=/usr/share/man --with-openssl \
      --build=arm
  fi
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[ike-scan]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ike-scan    # ad-hoc run, no install
nix profile install nixpkgs#ike-scan   # or persist it
```
