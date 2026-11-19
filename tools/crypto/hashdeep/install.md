---
tool: "hashdeep"
build-system: "autotools"
upstream: "https://github.com/jessek/hashdeep"
recipe: "blackarch"
nix-package: "hashdeep"
aur-package: "hashdeep"
source: "https://github.com/jessek/hashdeep"
license: "GPL, custom:unknown"
blackarch-pkgver: "4.4"
kali-package: "hashdeep"
kali-version: "4.4+git20170824+ds-1"
---

# Installing hashdeep

> Cross-platform tools to message digests for any number of files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hashdeep) (version `4.4`, license GPL, custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S hashdeep    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install hashdeep    # Kali package version 4.4+git20170824+ds-1
```

## Any Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/jessek/hashdeep/commit/6ef69a26126ee4e69a25392fd456b8a66c51dffd.patch
cd 6ef69a26126ee4e69a25392fd456b8a66c51dffd.patch
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `gcc-libs`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[hashdeep]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#hashdeep    # ad-hoc run, no install
nix profile install nixpkgs#hashdeep   # or persist it
```
