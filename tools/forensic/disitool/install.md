---
tool: "disitool"
build-system: "scripts"
upstream: "https://blog.didierstevens.com/my-software/#disitool"
recipe: "blackarch"
source: "https://blog.didierstevens.com/my-software/#disitool"
license: "custom:unknown"
blackarch-pkgver: "0.4"
---

# Installing disitool

> Tool to work with Windows executables digital signatures.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/disitool) (version `0.4`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S disitool    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://blog.didierstevens.com/my-software/#disitool) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pefile`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[disitool]] · [[BlackArch Tools]]

