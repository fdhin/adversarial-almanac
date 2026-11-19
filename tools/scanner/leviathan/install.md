---
tool: "leviathan"
build-system: "scripts"
upstream: "https://github.com/leviathan-framework/leviathan"
recipe: "blackarch"
source: "https://github.com/leviathan-framework/leviathan"
license: "GPL"
blackarch-pkgver: "35.a1a1d8c"
---

# Installing leviathan

> A mass audit toolkit which has wide range service discovery, brute force, SQL injection detection and running custom exploit capabilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/leviathan) (version `35.a1a1d8c`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S leviathan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/leviathan-framework/leviathan) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-beautifulsoup4, python2-google-api-python-client, python2-lxml, python2-paramiko, python2-requests, python2-shodan (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[leviathan]] · [[BlackArch Tools]]

