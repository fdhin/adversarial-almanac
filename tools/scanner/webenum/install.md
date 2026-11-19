---
tool: "webenum"
build-system: "scripts"
upstream: "https://github.com/sarthakpandit/webenum"
recipe: "blackarch"
source: "https://github.com/sarthakpandit/webenum"
license: "GPL"
blackarch-pkgver: "21.24b43b4"
---

# Installing webenum

> Tool to enumerate http responses using dynamically generated queries and more.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/webenum) (version `21.24b43b4`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S webenum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/sarthakpandit/webenum) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[webenum]] · [[BlackArch Tools]]

