---
tool: "jwt-key-recovery"
build-system: "scripts"
upstream: "https://github.com/FlorianPicca/JWT-Key-Recovery"
recipe: "blackarch"
source: "https://github.com/FlorianPicca/JWT-Key-Recovery"
license: "custom:unknown"
blackarch-pkgver: "11.094df95"
---

# Installing jwt-key-recovery

> Recovers the public key used to sign JWT tokens.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jwt-key-recovery) (version `11.094df95`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S jwt-key-recovery    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/FlorianPicca/JWT-Key-Recovery) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `fastecdsa`, `gmpy2`, `pycryptodome`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[jwt-key-recovery]] · [[BlackArch Tools]]

