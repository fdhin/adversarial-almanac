---
tool: "ipobfuscator"
build-system: "other"
upstream: "https://github.com/OsandaMalith/IPObfuscator"
recipe: "blackarch"
source: "https://github.com/OsandaMalith/IPObfuscator"
license: "custom:unknown"
blackarch-pkgver: "27.f005262"
---

# Installing ipobfuscator

> A simple tool to convert the IP to a DWORD IP.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ipobfuscator) (version `27.f005262`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ipobfuscator    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/OsandaMalith/IPObfuscator.git
cd IPObfuscator
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  gcc ip.c -o $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[ipobfuscator]] · [[BlackArch Tools]]

