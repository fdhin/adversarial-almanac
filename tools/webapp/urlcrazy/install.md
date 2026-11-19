---
tool: "urlcrazy"
build-system: "scripts"
upstream: "http://www.morningstarsecurity.com/research/urlcrazy"
recipe: "blackarch"
aur-package: "urlcrazy"
source: "http://www.morningstarsecurity.com/research/urlcrazy"
license: "custom"
blackarch-pkgver: "v0.8.2.r0.g78bf6e8"
---

# Installing urlcrazy

> Generate and test domain typos and variations to detect and perform typo squatting, URL hijacking, phishing, and corporate espionage.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/urlcrazy) (version `v0.8.2.r0.g78bf6e8`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S urlcrazy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.morningstarsecurity.com/research/urlcrazy) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-bundler`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[urlcrazy]] · [[BlackArch Tools]]

