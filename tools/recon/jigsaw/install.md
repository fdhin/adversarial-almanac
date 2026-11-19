---
tool: "jigsaw"
ebuild-package: "jigsaw"
build-system: "scripts"
upstream: "https://github.com/pentestgeek/${pkgname}"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/jigsaw"
version: "1.3"
license: "GPL3"
---

# Installing jigsaw

> A simple ruby script for enumerating information about a company's employees. It is useful for Social Engineering or Email Phishing. Source no long on GitHub available as tarball in ours.

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/jigsaw) (version `1.3`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S jigsaw
```

The same PKGBUILD is usually on the AUR (`paru -S jigsaw`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/pentestgeek/${pkgname}) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `ruby`

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[recon]] · [[jigsaw]] · [[ArchStrike Gap Analysis]]
