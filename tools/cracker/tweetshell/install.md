---
tool: "tweetshell"
build-system: "scripts"
upstream: "https://github.com/thelinuxchoice/tweetshell"
recipe: "blackarch"
source: "https://github.com/thelinuxchoice/tweetshell"
license: "GPL3"
blackarch-pkgver: "21.47a415c"
---

# Installing tweetshell

> Multi-thread Twitter BruteForcer in Shell Script.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tweetshell) (version `21.47a415c`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S tweetshell    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/thelinuxchoice/tweetshell) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `curl`
  - `tor`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[tweetshell]] · [[BlackArch Tools]]

