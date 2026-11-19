---
tool: "bindead"
build-system: "java"
upstream: "https://bitbucket.org/mihaila/bindead"
recipe: "blackarch"
source: "https://bitbucket.org/mihaila/bindead"
license: "GPL3"
blackarch-pkgver: "4504.67019b97b"
---

# Installing bindead

> A static analysis tool for binaries

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bindead) (version `4504.67019b97b`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S bindead    # needs the [blackarch] repo enabled
```

## Any Linux distro

Needs a JRE/JDK (`java` 11+ recommended).

**Source:** download the release from the [upstream site](https://bitbucket.org/mihaila/bindead) (the recipe uses a versioned archive).

```bash
# build per recipe steps below, then run:
java -jar <tool>.jar
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`, `maven`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `java-runtime`

## Exact build steps from the recipe

```bash
cd $pkgname

  unset _JAVA_OPTIONS

  export MAVEN_OPTS="-Dmaven.repo.local=$srcdir -Xms256m -Xmx512m"

  ./build.sh
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[bindead]] · [[BlackArch Tools]]

