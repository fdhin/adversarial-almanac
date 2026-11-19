---
tool: "jwtcat"
build-system: "scripts"
upstream: "https://github.com/aress31/jwtcat"
recipe: "blackarch"
nix-package: "jwtcat"
source: "https://github.com/aress31/jwtcat"
license: "Apache"
blackarch-pkgver: "77.f80f3d9"
---

# Installing jwtcat

> Script performs offline brute-force attacks against JSON Web Token (JWT)

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jwtcat) (version `77.f80f3d9`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S jwtcat    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/aress31/jwtcat) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `coloredlogs`, `pyjwt`, `tqdm`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[jwtcat]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#jwtcat    # ad-hoc run, no install
nix profile install nixpkgs#jwtcat   # or persist it
```
