---
tool: "spooftooph"
build-system: "make"
upstream: "http://www.hackfromacave.com/projects/spooftooph.html"
recipe: "blackarch"
nix-package: "spooftooph"
aur-package: "spooftooph"
source: "http://www.hackfromacave.com/projects/spooftooph.html"
license: "GPL"
blackarch-pkgver: "0.5.2"
kali-package: "spooftooph"
kali-version: "0.5.2-1kali4"
---

# Installing spooftooph

> Designed to automate spoofing or cloning Bluetooth device Name, Class, and Address. Cloning this information effectively allows Bluetooth device to hide in plain sight.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/spooftooph) (version `0.5.2`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S spooftooph    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install spooftooph    # Kali package version 0.5.2-1kali4
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.hackfromacave.com/projects/spooftooph.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `make`
- **System libraries** (Arch name — translate for your distro):
  - `bluez`
  - `bluez-libs` — Debian libbluetooth-dev / Fedora bluez-libs-devel
  - `ncurses`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[bluetooth]] · [[spooftooph]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#spooftooph    # ad-hoc run, no install
nix profile install nixpkgs#spooftooph   # or persist it
```
