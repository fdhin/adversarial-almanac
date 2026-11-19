---
tool: "goldeneye"
build-system: "scripts"
upstream: "https://github.com/jseidl/GoldenEye"
recipe: "blackarch"
source: "https://github.com/jseidl/GoldenEye"
license: "GPL3"
blackarch-pkgver: "28.792862f"
---

# Installing goldeneye

> A HTTP DoS test tool. Attack Vector exploited: HTTP Keep Alive + NoCache.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/goldeneye) (version `28.792862f`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S goldeneye    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/jseidl/GoldenEye.git
cd GoldenEye
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[dos]] · [[goldeneye]] · [[BlackArch Tools]]

