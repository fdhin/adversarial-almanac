---
tool: "spotbugs"
build-system: "java"
upstream: "https://github.com/spotbugs/spotbugs"
recipe: "blackarch"
source: "https://github.com/spotbugs/spotbugs"
license: "LGPL"
blackarch-pkgver: "4.10.5.r19.g6e0a5b5"
---

# Installing spotbugs

> A tool for static analysis to look for bugs in Java code.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/spotbugs) (version `4.10.5.r19.g6e0a5b5`, license LGPL).

## Arch / BlackArch

```bash
sudo pacman -S spotbugs    # needs the [blackarch] repo enabled
```

## Any Linux distro

Needs a JRE/JDK (`java` 11+ recommended).

**Source:** download the release from the [upstream site](https://github.com/spotbugs/spotbugs) (the recipe uses a versioned archive).

```bash
# build per recipe steps below, then run:
java -jar <tool>.jar
```

## Dependencies (from recipe)

- **Build tools**: `git`, `gradle`
- **System libraries** (Arch name — translate for your distro):
  - `java-environment=11`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./gradlew build -x test
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[spotbugs]] · [[BlackArch Tools]]

