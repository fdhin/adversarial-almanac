---
tool: "instashell"
build-system: "scripts"
upstream: "https://github.com/maxrooted/instashell"
recipe: "blackarch"
source: "https://github.com/maxrooted/instashell"
license: "GPL3"
blackarch-pkgver: "44.8f867bd"
---

# Installing instashell

> Multi-threaded Instagram Brute Forcer without password limit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/instashell) (version `44.8f867bd`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S instashell    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/maxrooted/instashell) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `curl`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `tor`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[instashell]] · [[BlackArch Tools]]

