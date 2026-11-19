---
tool: "stig-viewer"
build-system: "scripts"
upstream: "https://public.cyber.mil/stigs/stig-viewing-tools/"
recipe: "blackarch"
source: "https://public.cyber.mil/stigs/stig-viewing-tools/"
license: "custom:unknown"
blackarch-pkgver: "2.8"
---

# Installing stig-viewer

> XCCDF formatted SRGs and STIGs files viewer for SCAP validation tools.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/stig-viewer) (version `2.8`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S stig-viewer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [DISA STIG viewing-tools site](https://public.cyber.mil/stigs/stig-viewing-tools/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[stig-viewer]] · [[BlackArch Tools]]

