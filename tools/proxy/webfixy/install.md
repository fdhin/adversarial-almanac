---
tool: "webfixy"
build-system: "scripts"
upstream: "https://github.com/takeshixx/webfixy"
recipe: "blackarch"
source: "https://github.com/takeshixx/webfixy"
license: "GPL3"
blackarch-pkgver: "25.5d477b0"
---

# Installing webfixy

> On-the-fly decryption proxy for MikroTik RouterOS WebFig sessions.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/webfixy) (version `25.5d477b0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S webfixy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/takeshixx/webfixy) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `aiohttp`, `demjson`, `passlib`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[proxy]] · [[webfixy]] · [[BlackArch Tools]]

