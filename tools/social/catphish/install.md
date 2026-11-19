---
tool: "catphish"
build-system: "scripts"
upstream: "https://github.com/ring0lab/catphish"
recipe: "blackarch"
source: "https://github.com/ring0lab/catphish"
license: "custom:unknown"
blackarch-pkgver: "44.768d213"
---

# Installing catphish

> For phishing and corporate espionage.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/catphish) (version `44.768d213`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S catphish    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ring0lab/catphish) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `ruby`
  - `ruby-bundler`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[catphish]] · [[BlackArch Tools]]

