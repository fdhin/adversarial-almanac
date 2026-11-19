---
tool: "poracle"
build-system: "scripts"
upstream: "https://github.com/iagox86/poracle"
recipe: "blackarch"
source: "https://github.com/iagox86/poracle"
license: "BSD"
blackarch-pkgver: "68.dcc00b0"
---

# Installing poracle

> A tool for demonstrating padding oracle attacks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/poracle) (version `68.dcc00b0`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S poracle    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/iagox86/poracle) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-httparty`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[poracle]] · [[BlackArch Tools]]

