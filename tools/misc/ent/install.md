---
tool: "ent"
build-system: "make"
upstream: "http://www.fourmilab.ch/random"
recipe: "blackarch"
nix-package: "ent"
aur-package: "ent"
source: "http://www.fourmilab.ch/random/random.zip"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing ent

> Pseudorandom number sequence test.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ent) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ent    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
curl -fLO http://www.fourmilab.ch/random/random.zip
tar xf random.zip
cd random
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[ent]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ent    # ad-hoc run, no install
nix profile install nixpkgs#ent   # or persist it
```
