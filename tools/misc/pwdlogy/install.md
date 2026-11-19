---
tool: "pwdlogy"
build-system: "scripts"
upstream: "https://github.com/tch1001/pwdlogy"
recipe: "blackarch"
source: "https://github.com/tch1001/pwdlogy"
license: "custom:unknown"
blackarch-pkgver: "14.8b92bcf"
---

# Installing pwdlogy

> A target specific wordlist generating tool for social engineers and security researchers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pwdlogy) (version `14.8b92bcf`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S pwdlogy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/tch1001/pwdlogy) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python3`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[pwdlogy]] · [[BlackArch Tools]]

