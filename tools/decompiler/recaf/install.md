---
tool: "recaf"
build-system: "java"
upstream: "https://github.com/Col-E/Recaf"
recipe: "blackarch"
aur-package: "recaf"
source: "https://github.com/Col-E/Recaf"
license: "MIT"
blackarch-pkgver: "4.0.0.alpha.r594.gd5888cd"
---

# Installing recaf

> Modern Java bytecode editor.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/recaf) (version `4.0.0.alpha.r594.gd5888cd`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S recaf    # needs the [blackarch] repo enabled
```

## Any Linux distro

Needs a JRE/JDK (`java` 11+ recommended).

```bash
git clone https://github.com/Col-E/Recaf.git
cd Recaf
# build per recipe steps below, then run:
java -jar <tool>.jar
```

## Dependencies (from recipe)

- **Build tools**: `git`, `gradle`, `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `java${_javaver}-openjdk`
  - `java-openjfx`
  - `java-runtime`

## Exact build steps from the recipe

```bash
cd $pkgname

  # How upstream builds
  # https://github.com/Col-E/Recaf/blob/master/.github/workflows/build.yml

  # Try either with
  # 1. gradle as makedepends + call gradle
  # 2. no gradle as makedepends + call ./gradlew
  # `./gradlew` wrapper forces a specific version of gradle that may not be
  # compatible with latest Java version
  # See https://docs.gradle.org/current/userguide/compatibility.html

  export JAVA_HOME="/usr/lib/jvm/java-${_javaver}-openjdk"
  export PATH="$JAVA_HOME/bin:$PATH"
  TARGET_VERSION=$_javaver gradle publish --stacktrace
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[decompiler]] · [[recaf]] · [[BlackArch Tools]]

