---
tool: "torctl"
build-system: "scripts"
upstream: "https://github.com/BlackArch/torctl"
recipe: "blackarch"
nix-package: "torctl"
source: "https://github.com/BlackArch/torctl"
license: "GPL3"
blackarch-pkgver: "0.5.7"
---

# Installing torctl

> Script to redirect all traffic through tor network including dns queries for anonymizing entire system.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/torctl) (version `0.5.7`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S torctl    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/BlackArch/torctl) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `curl`
  - `iproute2`
  - `iptables`
  - `macchanger`
  - `secure-delete`
  - `systemd`
  - `tor`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/bash-completion`

Backlinks: [[automation]] · [[torctl]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#torctl    # ad-hoc run, no install
nix profile install nixpkgs#torctl   # or persist it
```
