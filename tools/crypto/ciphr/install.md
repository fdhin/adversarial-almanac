---
tool: "ciphr"
build-system: "scripts"
upstream: "https://github.com/frohoff/ciphr"
recipe: "blackarch"
source: "https://github.com/frohoff/ciphr"
license: "MIT"
blackarch-pkgver: "127.5da7137"
---

# Installing ciphr

> A CLI tool for encoding, decoding, encryption, decryption, and hashing streams of data.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ciphr) (version `127.5da7137`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ciphr    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/frohoff/ciphr) (the recipe uses a versioned archive).

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

Backlinks: [[crypto]] · [[ciphr]] · [[BlackArch Tools]]

