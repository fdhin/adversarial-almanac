---
tool: "sign"
build-system: "java"
upstream: "https://github.com/appium/sign"
recipe: "blackarch"
source: "https://github.com/appium/sign"
license: "Apache"
blackarch-pkgver: "10.2dc4018"
---

# Installing sign

> Automatically signs an apk with the Android test certificate.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sign) (version `10.2dc4018`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S sign    # needs the [blackarch] repo enabled
```

## Any Linux distro

Needs a JRE/JDK (`java` 11+ recommended).

**Source:** download the release from the [upstream site](https://github.com/appium/sign) (the recipe uses a versioned archive).

```bash
# build per recipe steps below, then run:
java -jar <tool>.jar
```

## Dependencies (from recipe)

- **Build tools**: `git`, `maven`
- **System libraries** (Arch name — translate for your distro):
  - `java-runtime`

## Exact build steps from the recipe

```bash
cd $pkgname

	mvn package
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[sign]] · [[BlackArch Tools]]

