---
tool: "zaproxy"
build-system: "java"
upstream: "https://www.zaproxy.org/"
recipe: "arch-official/extra"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/zaproxy/-/raw/main/PKGBUILD"
version: "2.17.0"
license: "Apache-2.0"
---

# Installing zaproxy

> Testing tool for finding vulnerabilities in web applications

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/zaproxy/-/raw/main/PKGBUILD) (version `2.17.0`, license Apache-2.0).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S zaproxy
```

## Kali / Debian-based — same package name

```bash
sudo apt install zaproxy
```

## Any other Linux distro

Needs a JRE/JDK (`java` 11+ recommended).

```bash
git clone https://github.com/zaproxy/zaproxy.git
cd zaproxy
# build per recipe steps below, then run:
java -jar <tool>.jar
```

## Dependencies (from recipe)

- **Build tools**: `git`, `gradle`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `java-environment=${_java}`
  - `java-runtime`
  - `ttf-font`

## Exact build steps from the recipe

```bash
cd "$pkgname"
  export PATH="/usr/lib/jvm/java-${_java}-openjdk/bin:$PATH"
  export JAVA_HOME="/usr/lib/jvm/java-${_java}-openjdk"
  ./gradlew :zap:distLinux
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[recon]] · [[zaproxy]] · [[Kali Gap Analysis]]
