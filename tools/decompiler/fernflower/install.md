---
tool: "fernflower"
build-system: "java"
upstream: "https://github.com/fesh0r/fernflower"
recipe: "blackarch"
source: "https://github.com/fesh0r/fernflower"
license: "Apache"
blackarch-pkgver: "861.d23c5069"
---

# Installing fernflower

> An analytical decompiler for Java.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fernflower) (version `861.d23c5069`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S fernflower    # needs the [blackarch] repo enabled
```

## Any Linux distro

Needs a JRE/JDK (`java` 11+ recommended).

**Source:** download the release from the [upstream site](https://github.com/fesh0r/fernflower) (the recipe uses a versioned archive).

```bash
# build per recipe steps below, then run:
java -jar <tool>.jar
```

## Dependencies (from recipe)

- **Build tools**: `git`, `gradle`
- **System libraries** (Arch name — translate for your distro):
  - `jdk17-openjdk`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./gradlew
  ./gradlew build
  ./gradlew jar
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[decompiler]] · [[fernflower]] · [[BlackArch Tools]]

