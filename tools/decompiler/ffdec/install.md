---
tool: "ffdec"
build-system: "java"
upstream: "https://github.com/jindrapetrik/jpexs-decompiler"
recipe: "blackarch"
aur-package: "ffdec"
source: "https://github.com/jindrapetrik/jpexs-decompiler"
license: "GPL-3.0-or-later"
blackarch-pkgver: "26.2.1"
---

# Installing ffdec

> Open Source Flash SWF decompiler and editor.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ffdec) (version `26.2.1`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S ffdec    # needs the [blackarch] repo enabled
```

## Any Linux distro

Needs a JRE/JDK (`java` 11+ recommended).

**Source:** download the release from the [upstream site](https://github.com/jindrapetrik/jpexs-decompiler) (the recipe uses a versioned archive).

```bash
# build per recipe steps below, then run:
java -jar <tool>.jar
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `ant`
  - `java-runtime`

## Exact build steps from the recipe

```bash
cd "jpexs-decompiler-version$pkgver"

    ant build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/applications`, `/usr/share/java`, `/usr/share/pixmaps`

Backlinks: [[decompiler]] · [[ffdec]] · [[BlackArch Tools]]

