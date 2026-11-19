---
tool: "username-anarchy"
build-system: "scripts"
upstream: "http://www.morningstarsecurity.com/research/username-anarchy"
recipe: "blackarch"
nix-package: "username-anarchy"
source: "http://www.morningstarsecurity.com/research/username-anarchy"
license: "GPL3"
blackarch-pkgver: "72.e063191"
---

# Installing username-anarchy

> Tools for generating usernames when penetration testing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/username-anarchy) (version `72.e063191`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S username-anarchy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.morningstarsecurity.com/research/username-anarchy) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[username-anarchy]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#username-anarchy    # ad-hoc run, no install
nix profile install nixpkgs#username-anarchy   # or persist it
```
