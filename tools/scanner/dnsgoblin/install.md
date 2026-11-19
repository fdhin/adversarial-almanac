---
tool: "dnsgoblin"
build-system: "other"
upstream: "https://nullsecurity.net/tools/scanner.html"
recipe: "blackarch"
source: "https://nullsecurity.net/tools/scanner.html"
license: "custom"
blackarch-pkgver: "0.1"
---

# Installing dnsgoblin

> Nasty creature constantly searching for DNS servers. It uses standard dns querys and waits for the replies.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnsgoblin) (version `0.1`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S dnsgoblin    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://nullsecurity.net/tools/scanner.html) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
gcc -Wall -Wextra -pedantic --std=gnu99 $CFLAGS -lpthread \
    -D_REENTRANT -o $pkgname "$pkgname.c"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[dnsgoblin]] · [[BlackArch Tools]]

