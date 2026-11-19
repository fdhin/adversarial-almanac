---
tool: "httpsscanner"
build-system: "scripts"
upstream: "https://code.google.com/p/libre-tools/"
recipe: "blackarch"
source: "https://web.archive.org/web/20220114092810/https://storage.googleapis.com/google-code-archive-downloads/v2/code.google.com/libre-tools/httpsScanner.jar"
license: "GPL-3.0-or-later"
blackarch-pkgver: "1.2"
---

# Installing httpsscanner

> A tool to test the strength of a SSL web server.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/httpsscanner) (version `1.2`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S httpsscanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/p/libre-tools/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[httpsscanner]] · [[BlackArch Tools]]

