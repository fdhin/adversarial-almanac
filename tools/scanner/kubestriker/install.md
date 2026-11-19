---
tool: "kubestriker"
build-system: "scripts"
upstream: "https://github.com/vchinnipilli/kubestriker"
recipe: "blackarch"
source: "https://github.com/vchinnipilli/kubestriker"
license: "Apache-2.0"
blackarch-pkgver: "v1.2.0.r2.ge1776ea"
---

# Installing kubestriker

> A Blazing fast Security Auditing tool for Kubernetes.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kubestriker) (version `v1.2.0.r2.ge1776ea`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S kubestriker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/vchinnipilli/kubestriker) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `virtualenv`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[kubestriker]] · [[BlackArch Tools]]

