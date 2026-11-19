---
tool: "nmap-parse-output"
build-system: "scripts"
upstream: "https://github.com/hahwul/nmap-parse-output"
recipe: "blackarch"
aur-package: "nmap-parse-output"
source: "https://github.com/hahwul/nmap-parse-output"
license: "BSD"
blackarch-pkgver: "26.a8e31b3"
---

# Installing nmap-parse-output

> Converts/manipulates/extracts data from a nmap scan output.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nmap-parse-output) (version `26.a8e31b3`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S nmap-parse-output    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/hahwul/nmap-parse-output) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[nmap-parse-output]] · [[BlackArch Tools]]

