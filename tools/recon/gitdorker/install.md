---
tool: "gitdorker"
build-system: "scripts"
upstream: "https://github.com/obheda12/GitDorker"
recipe: "blackarch"
source: "https://github.com/obheda12/GitDorker"
license: "custom:unknown"
blackarch-pkgver: "113.8199375"
---

# Installing gitdorker

> Python program to scrape secrets from GitHub through usage of a large repository of dorks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gitdorker) (version `113.8199375`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S gitdorker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/obheda12/GitDorker) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `termcolor`, `tqdm`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[gitdorker]] · [[BlackArch Tools]]

