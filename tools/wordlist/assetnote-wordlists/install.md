---
tool: "assetnote-wordlists"
build-system: "scripts"
upstream: "https://github.com/assetnote/wordlists"
recipe: "blackarch"
source: "https://github.com/assetnote/wordlists"
license: "Apache-2.0"
blackarch-pkgver: "183.6dea231"
---

# Installing assetnote-wordlists

> Assetnote generated wordlists.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/assetnote-wordlists) (version `183.6dea231`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S assetnote-wordlists    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/assetnote/wordlists.git
cd wordlists
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wordlist]] · [[assetnote-wordlists]] · [[BlackArch Tools]]

