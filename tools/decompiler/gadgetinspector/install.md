---
tool: "gadgetinspector"
build-system: "java"
upstream: "https://github.com/JackOfMostTrades/gadgetinspector"
recipe: "blackarch"
source: "https://github.com/JackOfMostTrades/gadgetinspector"
license: "MIT"
blackarch-pkgver: "6.ac7832d"
---

# Installing gadgetinspector

> A byte code analyzer for finding deserialization gadget chains in Java applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gadgetinspector) (version `6.ac7832d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S gadgetinspector    # needs the [blackarch] repo enabled
```

## Any Linux distro

Needs a JRE/JDK (`java` 11+ recommended).

**Source:** download the release from the [upstream site](https://github.com/JackOfMostTrades/gadgetinspector) (the recipe uses a versioned archive).

```bash
# build per recipe steps below, then run:
java -jar <tool>.jar
```

## Dependencies (from recipe)

- **Build tools**: `git`, `gradle`, `jdk-openjdk`
- **System libraries** (Arch name — translate for your distro):
  - `apache-ant`
  - `java-runtime`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./gradlew shadowJar
  #./gradlew
  #./gradlew build
  #./gradlew jar
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[decompiler]] · [[gadgetinspector]] · [[BlackArch Tools]]

