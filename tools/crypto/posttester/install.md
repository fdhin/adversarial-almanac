---
tool: "posttester"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/109010/MagicHash-Collision-Testing-Tool.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/UNIX/scanners/PostTester.jar"
license: "unknown"
blackarch-pkgver: "0.1"
---

# Installing posttester

> A jar file that will send POST requests to servers in order to test for the hash collision vulnerability discussed at the Chaos Communication Congress in Berlin.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/posttester) (version `0.1`, license unknown).

## Arch / BlackArch

```bash
sudo pacman -S posttester    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/109010/MagicHash-Collision-Testing-Tool.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[posttester]] · [[BlackArch Tools]]

