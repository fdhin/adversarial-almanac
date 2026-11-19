---
tool: "pwncat"
build-system: "scripts"
upstream: "https://github.com/cytopia/pwncat"
recipe: "blackarch"
nix-package: "pwncat"
aur-package: "pwncat"
source: "https://github.com/cytopia/pwncat"
license: "MIT"
blackarch-pkgver: "0.1.2"
---

# Installing pwncat

> Bind and reverse shell handler with FW/IDS/IPS evasion, self-inject and port-scanning.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pwncat) (version `0.1.2`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pwncat    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/cytopia/pwncat) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `setuptools`, `wheel`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[backdoor]] · [[pwncat]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#pwncat    # ad-hoc run, no install
nix profile install nixpkgs#pwncat   # or persist it
```
