---
tool: "pwnat"
build-system: "make"
upstream: "http://samy.pl/pwnat/"
recipe: "blackarch"
nix-package: "pwnat"
aur-package: "pwnat"
source: "http://samy.pl/pwnat/"
license: "GPL"
blackarch-pkgver: "47.3a032f9"
kali-package: "pwnat"
kali-version: "0.3.0-0kali2"
---

# Installing pwnat

> A tool that allows any number of clients behind NATs to communicate with a server behind a separate NAT with *no* port forwarding and *no* DMZ setup on any routers in order to directly communicate with each other.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pwnat) (version `47.3a032f9`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S pwnat    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install pwnat    # Kali package version 0.3.0-0kali2
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://samy.pl/pwnat/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[pwnat]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#pwnat    # ad-hoc run, no install
nix profile install nixpkgs#pwnat   # or persist it
```
