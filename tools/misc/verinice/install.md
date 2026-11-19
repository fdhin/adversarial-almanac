---
tool: "verinice"
build-system: "java"
upstream: "https://github.com/SerNet/verinice"
recipe: "blackarch"
aur-package: "verinice"
source: "https://github.com/SerNet/verinice"
license: "GPL3"
blackarch-pkgver: "1.29.0.r0.g15eae36d0"
provides: ["vDesigner", "verinice"]
---

# Installing verinice

> Tool for managing information security.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/verinice) (version `1.29.0.r0.g15eae36d0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S verinice    # needs the [blackarch] repo enabled
```

## Any Linux distro

Needs a JRE/JDK (`java` 11+ recommended).

**Source:** download the release from the [upstream site](https://github.com/SerNet/verinice) (the recipe uses a versioned archive).

```bash
# build per recipe steps below, then run:
java -jar <tool>.jar
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`, `maven`
- **System libraries** (Arch name — translate for your distro):
  - `jdk8-openjdk`
  - `unzip`
  - `which`

## Exact build steps from the recipe

```bash
cd $pkgname

  export JAVA_HOME='/usr/lib/jvm/java-8-openjdk/'
  ./mvnw -Dosgi.parentClassloader=ext -Dmaven.antrun.skip=true \
    -Dtycho.disableP2Mirrors=true clean verify
```

## Installed layout (per recipe)

- Commands: `/usr/bin/vDesigner`, `/usr/bin/verinice`

Backlinks: [[misc]] · [[verinice]] · [[BlackArch Tools]]

