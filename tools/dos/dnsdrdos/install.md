---
tool: "dnsdrdos"
build-system: "other"
upstream: "http://nullsecurity.net/tools/dos.html"
recipe: "blackarch"
source: "https://raw.githubusercontent.com/nullsecuritynet/tools/master/dos/dnsdrdos/source/dnsdrdos.c"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing dnsdrdos

> Proof of concept code for distributed DNS reflection DoS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnsdrdos) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dnsdrdos    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://nullsecurity.net/tools/dos.html) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
gcc -Wall $CFLAGS -o $pkgname "$pkgname.c"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[dos]] · [[dnsdrdos]] · [[BlackArch Tools]]

