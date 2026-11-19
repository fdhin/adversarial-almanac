---
tool: "ghidra"
build-system: "java"
upstream: "https://ghidra-sre.org/"
recipe: "arch-official/extra"
nix-package: "ghidra"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/ghidra/-/raw/main/PKGBUILD"
version: "12.1.2"
license: "unknown"
---

# Installing ghidra

> Software Reverse Engineering Framework

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/ghidra/-/raw/main/PKGBUILD) (version `12.1.2`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S ghidra
```

## Kali / Debian-based — same package name

```bash
sudo apt install ghidra
```

## Nix (works on any distro)

```bash
nix run nixpkgs#ghidra    # ad-hoc run, no install
nix profile install nixpkgs#ghidra   # or persist it
```

## Any other Linux distro

Needs a JRE/JDK (`java` 11+ recommended).

```bash
git clone https://github.com/NationalSecurityAgency/ghidra-data
cd ghidra-data
# build per recipe steps below, then run:
java -jar <tool>.jar
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `java-environment=21`

## Exact build steps from the recipe

```bash
cd ghidra-Ghidra_${pkgver}_build

  export GRADLE_HOME="$(pwd)/usr/share/java/gradle/"
  export PATH="/usr/lib/jvm/java-21-openjdk/bin:${PATH}"
  export PATH="$(pwd)/usr/share/java/gradle/bin/:${PATH}"

  # Build dependencies
  _build_python_dependency protobuf-${_protobuf} Debugger-rmi-trace
  _build_python_dependency setuptools-${_setuptools} Debugger-rmi-trace PyGhidra
  _build_python_dependency wheel-${_wheel} Debugger-rmi-trace PyGhidra
  _build_python_dependency packaging-${_packaging} PyGhidra
  _build_python_dependency jpype1-${_jpype1} PyGhidra

  # Ghidra build
  gradle yajswDevUnpack
  gradle buildNatives_linux_x86_64
  gradle sleighCompile
  gradle buildGhidra
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout
- Data: `/usr/share/icons`

Backlinks: [[defensive]] · [[ghidra]] · [[Kali Gap Analysis]]
