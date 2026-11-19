---
tool: "morxbtcrack"
build-system: "scripts"
upstream: "http://www.morxploit.com/tools/"
recipe: "blackarch"
source: "http://web.archive.org/web/20160905190208/http://www.morxploit.com:80/morxtools/MorXBTCrack"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing morxbtcrack

> Single Bitcoin private key cracking tool released.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/morxbtcrack) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S morxbtcrack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.morxploit.com/tools/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `morxkeyfmt`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[morxbtcrack]] · [[BlackArch Tools]]

