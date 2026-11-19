---
tool: "vega"
ebuild-package: "vega"
build-system: "other"
upstream: "https://github.com/subgraph/Vega"
recipe: "archstrike-pkgbuild"
aur-package: "vega"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/vega"
version: "1.0.devel.130"
license: "GPL2"
---

# Installing vega

> An open source platform to test the security of web applications

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/vega) (version `1.0.devel.130`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S vega
```

Also on the AUR: `paru -S vega` (version `2.1.0-1`).

## Any other Linux distro

**From source:**

```bash
curl -fLO https://support.subgraph.com/downloads/VegaBuild-linux.gtk.x86.zip
tar xf VegaBuild-linux.gtk.x86.zip
cd VegaBuild-linux.gtk.x86
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `unzip`
  - `webkit2gtk`

## Exact build steps from the recipe

```bash
cd ${srcdir}
  if [[ "${CARCH}" == x86_64 ]]; then
    unzip -oq Vega*x86_64.zip -d vega
  else
    unzip -oq Vega*x86.zip -d vega
  fi
```

## Installed layout (per recipe)

- Commands: `/usr/bin/vega`

Backlinks: [[webapp]] · [[vega]] · [[ArchStrike Gap Analysis]]
