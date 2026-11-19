---
tool: "ngrok"
build-system: "scripts"
upstream: "https://ngrok.com/"
recipe: "blackarch"
nix-package: "ngrok"
aur-package: "ngrok"
source: "https://ngrok.com/"
license: "LicenseRef-custom"
blackarch-pkgver: "3.33.1"
---

# Installing ngrok

> A tunneling, reverse proxy for developing and understanding networked, HTTP services.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ngrok) (version `3.33.1`, license LicenseRef-custom).

## Arch / BlackArch

```bash
sudo pacman -S ngrok    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://ngrok.com/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/bash-completion`, `/usr/share/zsh`

Backlinks: [[tunnel]] · [[ngrok]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ngrok    # ad-hoc run, no install
nix profile install nixpkgs#ngrok   # or persist it
```
