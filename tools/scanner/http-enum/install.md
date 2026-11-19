---
tool: "http-enum"
build-system: "scripts"
upstream: "https://www.thexero.co.uk/tools/http-enum/"
recipe: "blackarch"
source: "https://www.thexero.co.uk/tools/http-enum/"
license: "custom:unknown"
blackarch-pkgver: "0.4"
---

# Installing http-enum

> A tool to enumerate the enabled HTTP methods supported on a webserver.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/http-enum) (version `0.4`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S http-enum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.thexero.co.uk/tools/http-enum/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[http-enum]] · [[BlackArch Tools]]

