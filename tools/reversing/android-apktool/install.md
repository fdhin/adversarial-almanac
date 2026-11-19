---
tool: "android-apktool"
build-system: "java"
upstream: "https://github.com/iBotPeaches/Apktool/releases"
recipe: "blackarch"
aur-package: "android-apktool"
source: "https://github.com/iBotPeaches/Apktool/releases"
license: "Apache"
blackarch-pkgver: "2.7.0"
provides: ["apktool"]
---

# Installing android-apktool

> A tool for reverse engineering Android apk files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/android-apktool) (version `2.7.0`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S android-apktool    # needs the [blackarch] repo enabled
```

## Any Linux distro

Needs a JRE/JDK (`java` 11+ recommended).

**Source:** download the release from the [upstream site](https://github.com/iBotPeaches/Apktool/releases) (the recipe uses a versioned archive).

```bash
# build per recipe steps below, then run:
java -jar <tool>.jar
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `jdk17-openjdk`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  ./gradlew build shadowJar
```

## Installed layout (per recipe)

- Commands: `/usr/bin/apktool`

Backlinks: [[reversing]] · [[android-apktool]] · [[BlackArch Tools]]

