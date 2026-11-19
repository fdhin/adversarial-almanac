---
tool: "hdcp-genkey"
build-system: "scripts"
upstream: "https://github.com/rjw57/hdcp-genkey"
recipe: "blackarch"
source: "https://github.com/rjw57/hdcp-genkey"
license: "custom:unknown"
blackarch-pkgver: "18.e8d342d"
---

# Installing hdcp-genkey

> Generate HDCP source and sink keys from the leaked master key.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hdcp-genkey) (version `18.e8d342d`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S hdcp-genkey    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/rjw57/hdcp-genkey) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-simplejson (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[hdcp-genkey]] · [[BlackArch Tools]]

