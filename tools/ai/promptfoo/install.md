---
tool: "promptfoo"
build-system: "node"
upstream: "https://github.com/promptfoo/promptfoo"
recipe: "blackarch"
nix-package: "promptfoo"
aur-package: "promptfoo"
source: "https://github.com/promptfoo/promptfoo"
license: "MIT"
blackarch-pkgver: "0.120.20"
---

# Installing promptfoo

> Test and evaluate LLM outputs - AI red teaming, pentesting, and vulnerability scanning.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/promptfoo) (version `0.120.20`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S promptfoo    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `nodejs` + `npm`):**

**Source:** download the release from the [upstream site](https://github.com/promptfoo/promptfoo) (the recipe uses a versioned archive).

```bash
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `gcc`, `make`, `nodejs`, `npm`
- **System libraries** (Arch name — translate for your distro):
  - `jq`
  - `python`
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[ai]] · [[promptfoo]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#promptfoo    # ad-hoc run, no install
nix profile install nixpkgs#promptfoo   # or persist it
```
