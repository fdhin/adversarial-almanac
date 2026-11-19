---
tool: "c7decrypt"
build-system: "scripts"
upstream: "https://github.com/claudijd/c7decrypt"
recipe: "blackarch"
source: "https://github.com/claudijd/c7decrypt"
license: "custom:unknown"
blackarch-pkgver: "0.3.3"
---

# Installing c7decrypt

> Cisco password type encryptor and decryptor.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/c7decrypt) (version `0.3.3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S c7decrypt    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/claudijd/c7decrypt) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `ruby`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[c7decrypt]] · [[BlackArch Tools]]

