---
tool: "jadx"
build-system: "java"
upstream: "https://github.com/skylot/jadx"
recipe: "arch-official/extra"
nix-package: "jadx"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/jadx/-/raw/main/PKGBUILD"
version: "1.5.6"
license: "unknown"
---

# Installing jadx

> Dex to Java decompiler

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/jadx/-/raw/main/PKGBUILD) (version `1.5.6`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S jadx
```

## Kali / Debian-based — same package name

```bash
sudo apt install jadx
```

## Nix (works on any distro)

```bash
nix run nixpkgs#jadx    # ad-hoc run, no install
nix profile install nixpkgs#jadx   # or persist it
```

## Any other Linux distro

Needs a JRE/JDK (`java` 11+ recommended).

```bash
git clone https://github.com/skylot/jadx.git
cd jadx
# build per recipe steps below, then run:
java -jar <tool>.jar
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `java-environment=21`

## Exact build steps from the recipe

```bash
cd ${pkgname}-${pkgver}
  # https://github.com/skylot/jadx/blob/v1.5.4/build.gradle.kts#L14
  JADX_VERSION=${pkgver} gradle dist
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout
- Data: `/usr/share/icons`

Backlinks: [[forensic]] · [[jadx]] · [[Kali Gap Analysis]]
