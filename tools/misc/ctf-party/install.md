---
tool: "ctf-party"
build-system: "ruby"
upstream: "https://noraj.github.io/ctf-party/"
recipe: "blackarch"
source: "https://noraj.github.io/ctf-party/"
license: "MIT"
blackarch-pkgver: "5.0.0.r58.g8dfd12a"
---

# Installing ctf-party

> A CLI tool & library to enhance and speed up script/exploit writing for CTF players.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ctf-party) (version `5.0.0.r58.g8dfd12a`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ctf-party    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `ruby`):**

**Source:** download the release from the [upstream site](https://noraj.github.io/ctf-party/) (the recipe uses a versioned archive).

```bash
bundle install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-bundler`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[ctf-party]] · [[BlackArch Tools]]

