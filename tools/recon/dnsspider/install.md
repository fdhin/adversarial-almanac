---
tool: "dnsspider"
build-system: "scripts"
upstream: "http://nullsecurity.net/tools/scanner.html"
recipe: "blackarch"
source: "http://nullsecurity.net/tools/scanner.html"
license: "MIT"
blackarch-pkgver: "1.5"
---

# Installing dnsspider

> A fast multithreaded bruteforcer of subdomains that leverages a wordlist and/or character permutation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnsspider) (version `1.5`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S dnsspider    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://nullsecurity.net/tools/scanner.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `dnspython`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[dnsspider]] · [[BlackArch Tools]]

