---
tool: "basedomainname"
build-system: "scripts"
upstream: "http://www.morningstarsecurity.com/research"
recipe: "blackarch"
source: "http://www.morningstarsecurity.com/research"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing basedomainname

> Tool that can extract TLD (Top Level Domain), domain extensions (Second Level Domain + TLD), domain name, and hostname from fully qualified domain names.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/basedomainname) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S basedomainname    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.morningstarsecurity.com/research) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `ruby`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[basedomainname]] · [[BlackArch Tools]]

