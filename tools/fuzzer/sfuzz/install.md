---
tool: "sfuzz"
build-system: "autotools"
upstream: "http://aconole.brad-x.com/programs/sfuzz.html"
recipe: "blackarch"
source: "http://aconole.brad-x.com/programs/sfuzz.html"
license: "BSD"
blackarch-pkgver: "201.9c01dab"
kali-package: "sfuzz"
kali-version: "0.7.0-1kali4"
---

# Installing sfuzz

> A simple fuzzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sfuzz) (version `201.9c01dab`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S sfuzz    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install sfuzz    # Kali package version 0.7.0-1kali4
```

## Any Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/orgcandman/Simple-Fuzzer.git
cd Simple-Fuzzer
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[fuzzer]] · [[sfuzz]] · [[BlackArch Tools]]

