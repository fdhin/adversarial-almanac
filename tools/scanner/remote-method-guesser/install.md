---
tool: "remote-method-guesser"
build-system: "java"
upstream: "https://github.com/qtc-de/remote-method-guesser"
recipe: "blackarch"
source: "https://github.com/qtc-de/remote-method-guesser"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v5.1.0.r11.g3398ec6"
provides: ["rmg"]
---

# Installing remote-method-guesser

> Java RMI vulnerability scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/remote-method-guesser) (version `v5.1.0.r11.g3398ec6`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S remote-method-guesser    # needs the [blackarch] repo enabled
```

## Any Linux distro

Needs a JRE/JDK (`java` 11+ recommended).

**Source:** download the release from the [upstream site](https://github.com/qtc-de/remote-method-guesser) (the recipe uses a versioned archive).

```bash
# build per recipe steps below, then run:
java -jar <tool>.jar
```

## Dependencies (from recipe)

- **Build tools**: `git`, `maven`
- **System libraries** (Arch name — translate for your distro):
  - `java-runtime`
  - `ysoserial`

## Exact build steps from the recipe

```bash
cd $pkgname

  mvn clean package -DskipTests
```

## Installed layout (per recipe)

- Commands: `/usr/bin/rmg`
- Data: `/usr/share/bash-completion`

Backlinks: [[scanner]] · [[remote-method-guesser]] · [[BlackArch Tools]]

