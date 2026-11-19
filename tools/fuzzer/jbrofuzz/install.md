---
tool: "jbrofuzz"
build-system: "scripts"
upstream: "http://sourceforge.net/projects/jbrofuzz/"
recipe: "blackarch"
source: "http://sourceforge.net/projects/jbrofuzz/"
license: "GPL"
blackarch-pkgver: "2.5"
---

# Installing jbrofuzz

> Web application protocol fuzzer that emerged from the needs of penetration testing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jbrofuzz) (version `2.5`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S jbrofuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/jbrofuzz/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[jbrofuzz]] · [[BlackArch Tools]]

