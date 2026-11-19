---
tool: "shellz"
build-system: "scripts"
upstream: "https://github.com/4ndr34z/shells"
recipe: "blackarch"
nix-package: "shellz"
source: "https://github.com/4ndr34z/shells"
license: "GPL3"
blackarch-pkgver: "161.0ed068f"
---

# Installing shellz

> A script for generating common revshells fast and easy.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shellz) (version `161.0ed068f`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S shellz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/4ndr34z/shells) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `updog`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `jq`
  - `ngrok`
  - `openbsd-netcat`
  - `rlwrap`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[shellz]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#shellz    # ad-hoc run, no install
nix profile install nixpkgs#shellz   # or persist it
```
