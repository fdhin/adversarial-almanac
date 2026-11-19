---
tool: "cloakify"
build-system: "scripts"
upstream: "https://github.com/trycatchhcf/cloakify"
recipe: "blackarch"
source: "https://github.com/trycatchhcf/cloakify"
license: "MIT"
blackarch-pkgver: "117.f45c3b3"
provides: ["decloakify"]
---

# Installing cloakify

> Data Exfiltration In Plain Sight; Evade DLP/MLS Devices; Social Engineering of Analysts; Evade AV Detection.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cloakify) (version `117.f45c3b3`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S cloakify    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/TryCatchHCF/Cloakify.git
cd Cloakify
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- Commands: `/usr/bin/decloakify`

Backlinks: [[misc]] · [[cloakify]] · [[BlackArch Tools]]

