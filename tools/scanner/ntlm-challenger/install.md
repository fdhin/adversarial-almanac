---
tool: "ntlm-challenger"
build-system: "scripts"
upstream: "https://github.com/b17zr/ntlm_challenger"
recipe: "blackarch"
nix-package: "ntlm-challenger"
source: "https://github.com/b17zr/ntlm_challenger"
license: "MIT"
blackarch-pkgver: "8.bd61ef6"
---

# Installing ntlm-challenger

> Parse NTLM over HTTP challenge messages.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ntlm-challenger) (version `8.bd61ef6`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ntlm-challenger    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/b17zr/ntlm_challenger) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[ntlm-challenger]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ntlm-challenger    # ad-hoc run, no install
nix profile install nixpkgs#ntlm-challenger   # or persist it
```
