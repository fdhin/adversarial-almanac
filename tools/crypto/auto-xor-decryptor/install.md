---
tool: "auto-xor-decryptor"
build-system: "scripts"
upstream: "https://github.com/MRGEffitas/scripts"
recipe: "blackarch"
source: "https://github.com/MRGEffitas/scripts"
license: "GPL"
blackarch-pkgver: "7.2eb176d"
---

# Installing auto-xor-decryptor

> Automatic XOR decryptor tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/auto-xor-decryptor) (version `7.2eb176d`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S auto-xor-decryptor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/MRGEffitas/scripts
cd scripts
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[auto-xor-decryptor]] · [[BlackArch Tools]]

