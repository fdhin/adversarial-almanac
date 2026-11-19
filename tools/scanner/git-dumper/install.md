---
tool: "git-dumper"
build-system: "scripts"
upstream: "https://github.com/arthaud/git-dumper"
recipe: "blackarch"
nix-package: "git-dumper"
source: "https://github.com/arthaud/git-dumper"
license: "MIT"
blackarch-pkgver: "1.0.9.r8.g2b47da1"
---

# Installing git-dumper

> A tool to dump a git repository from a website.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/git-dumper) (version `1.0.9.r8.g2b47da1`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S git-dumper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/arthaud/git-dumper) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `build`, `dulwich`, `pip`, `pysocks`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[git-dumper]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#git-dumper    # ad-hoc run, no install
nix profile install nixpkgs#git-dumper   # or persist it
```
