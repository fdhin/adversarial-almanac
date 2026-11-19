---
tool: "fuzzdiff"
build-system: "scripts"
upstream: "http://vsecurity.com/resources/tool/"
recipe: "blackarch"
source: "http://vsecurity.com/resources/tool/"
license: "GPL-3.0-or-later"
blackarch-pkgver: "1.1"
---

# Installing fuzzdiff

> A simple tool designed to help out with crash analysis during fuzz testing. It selectively 'un-fuzzes' portions of a fuzzed file that is known to cause a crash, re-launches the targeted application, and sees if it still crashes.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fuzzdiff) (version `1.1`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S fuzzdiff    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://vsecurity.com/resources/tool/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[fuzzdiff]] · [[BlackArch Tools]]

