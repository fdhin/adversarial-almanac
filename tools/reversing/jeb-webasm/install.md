---
tool: "jeb-webasm"
build-system: "scripts"
upstream: "https://www.pnfsoftware.com/jeb/#wasm"
recipe: "blackarch"
source: "https://www.pnfsoftware.com/dl?jebdemowasm"
license: "unknown"
blackarch-pkgver: "3.0.5.201811021648"
---

# Installing jeb-webasm

> WebAssembly decompiler.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jeb-webasm) (version `3.0.5.201811021648`, license n/a).

## Arch / BlackArch

```bash
sudo pacman -S jeb-webasm    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.pnfsoftware.com/jeb/#wasm) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `java-runtime-common`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[reversing]] · [[jeb-webasm]] · [[BlackArch Tools]]

