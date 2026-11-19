---
tool: "goofuzz"
build-system: "scripts"
upstream: "https://github.com/m3n0sd0n4ld/GooFuzz"
recipe: "blackarch"
source: "https://github.com/m3n0sd0n4ld/GooFuzz"
license: "GPL3"
blackarch-pkgver: "2.0.r1.gc32df39"
---

# Installing goofuzz

> A Bash script that uses advanced Google search techniques to obtain sensitive information in files or directories without making requests to the web server.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/goofuzz) (version `2.0.r1.gc32df39`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S goofuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/m3n0sd0n4ld/GooFuzz) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `curl`
  - `sed`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[goofuzz]] · [[BlackArch Tools]]

