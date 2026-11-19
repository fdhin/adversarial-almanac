---
tool: "android-backup-extractor"
build-system: "java"
upstream: "https://github.com/nelenkov/android-backup-extractor"
recipe: "blackarch"
nix-package: "android-backup-extractor"
aur-package: "android-backup-extractor"
source: "https://github.com/nelenkov/android-backup-extractor"
license: "Apache-2.0"
blackarch-pkgver: "latest.r0.g38fd634"
provides: ["abe"]
---

# Installing android-backup-extractor

> Android backup extractor

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/android-backup-extractor) (version `latest.r0.g38fd634`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S android-backup-extractor    # needs the [blackarch] repo enabled
```

## Any Linux distro

Needs a JRE/JDK (`java` 11+ recommended).

**Source:** download the release from the [upstream site](https://github.com/nelenkov/android-backup-extractor) (the recipe uses a versioned archive).

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

  mvn clean package -DskipTests
```

## Installed layout (per recipe)

- Commands: `/usr/bin/abe`

Backlinks: [[mobile]] · [[android-backup-extractor]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#android-backup-extractor    # ad-hoc run, no install
nix profile install nixpkgs#android-backup-extractor   # or persist it
```
