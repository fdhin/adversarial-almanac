---
tool: "blackeye"
build-system: "scripts"
upstream: "https://github.com/Git-Ankitraj/blackeye-im"
recipe: "blackarch"
source: "https://github.com/Git-Ankitraj/blackeye-im"
license: "GPL3"
blackarch-pkgver: "v2.5.r8.gbdc11a2"
---

# Installing blackeye

> Ultimate phishing tool with ngrok and serveo.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/blackeye) (version `v2.5.r8.gbdc11a2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S blackeye    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Git-Ankitraj/blackeye-im) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `curl`
  - `ngrok`
  - `php`
  - `unzip`
  - `wget`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[blackeye]] · [[BlackArch Tools]]

