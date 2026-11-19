---
tool: "cloud-audit"
build-system: "scripts"
upstream: "https://github.com/gebalamariusz/cloud-audit"
recipe: "blackarch"
source: "https://github.com/gebalamariusz/cloud-audit"
license: "MIT"
blackarch-pkgver: "2.5.0.r0.g0207aa0"
---

# Installing cloud-audit

> Open-source, read-only AWS security scanner. 110 checks across 25 services, Terraform remediation on every finding.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cloud-audit) (version `2.5.0.r0.g0207aa0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S cloud-audit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/gebalamariusz/cloud-audit) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[cloud-audit]] · [[BlackArch Tools]]

