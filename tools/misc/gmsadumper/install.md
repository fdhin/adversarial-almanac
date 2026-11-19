---
tool: "gmsadumper"
build-system: "scripts"
upstream: "https://github.com/micahvandeusen/gMSADumper"
recipe: "blackarch"
source: "https://github.com/micahvandeusen/gMSADumper"
license: "GPL3"
blackarch-pkgver: "18.e03187c"
---

# Installing gmsadumper

> A tool that Reads any gMSA password blobs the user can access and parses the values.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gmsadumper) (version `18.e03187c`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S gmsadumper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/micahvandeusen/gMSADumper) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `ldap3`, `pycryptodomex`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[gmsadumper]] · [[BlackArch Tools]]

