---
tool: "gitrecon"
build-system: "scripts"
upstream: "https://github.com/GONZOsint/gitrecon"
recipe: "blackarch"
source: "https://github.com/GONZOsint/gitrecon"
license: "custom:unknown"
blackarch-pkgver: "30.6467e78"
---

# Installing gitrecon

> OSINT tool to get information from a Github and Gitlab profile and find user's email addresses leaked on commits.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gitrecon) (version `30.6467e78`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S gitrecon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/GONZOsint/gitrecon) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `rich`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[gitrecon]] · [[BlackArch Tools]]

