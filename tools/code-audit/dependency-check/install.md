---
tool: "dependency-check"
build-system: "scripts"
upstream: "https://github.com/jeremylong/DependencyCheck/releases/"
recipe: "blackarch"
source: "https://github.com/jeremylong/DependencyCheck/releases/"
license: "APACHE"
blackarch-pkgver: "10.0.2"
---

# Installing dependency-check

> A tool that attempts to detect publicly disclosed vulnerabilities contained within a project's dependencies.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dependency-check) (version `10.0.2`, license APACHE).

## Arch / BlackArch

```bash
sudo pacman -S dependency-check    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/jeremylong/DependencyCheck/releases/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `bash`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[dependency-check]] · [[BlackArch Tools]]

