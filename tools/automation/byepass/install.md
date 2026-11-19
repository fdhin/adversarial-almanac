---
tool: "byepass"
build-system: "scripts"
upstream: "https://github.com/webpwnized/byepass"
recipe: "blackarch"
source: "https://github.com/webpwnized/byepass"
license: "custom:unknown"
blackarch-pkgver: "213.8cbfd9b"
provides: ["passtime"]
---

# Installing byepass

> Automates password cracking tasks using optimized dictionaries and mangling rules.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/byepass) (version `213.8cbfd9b`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S byepass    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/webpwnized/byepass) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `john`
  - `python`
  - `unzip`

## Installed layout (per recipe)

- Commands: `/usr/bin/passtime`

Backlinks: [[automation]] · [[byepass]] · [[BlackArch Tools]]

