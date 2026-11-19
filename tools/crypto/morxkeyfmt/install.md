---
tool: "morxkeyfmt"
build-system: "scripts"
upstream: "https://www.morxploit.com/tools/"
recipe: "blackarch"
source: "https://www.morxploit.com/morxtools/keyfmt"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing morxkeyfmt

> Read a private key from stdin and output formatted data values.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/morxkeyfmt) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S morxkeyfmt    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.morxploit.com/tools/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[morxkeyfmt]] · [[BlackArch Tools]]

