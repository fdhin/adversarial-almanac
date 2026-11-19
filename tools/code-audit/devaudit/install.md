---
tool: "devaudit"
build-system: "other"
upstream: "https://github.com/sonatype-nexus-community/DevAudit"
recipe: "blackarch"
source: "https://github.com/sonatype-nexus-community/DevAudit"
license: "BSD"
blackarch-pkgver: "803.ca0a68e"
---

# Installing devaudit

> An open-source, cross-platform, multi-purpose security auditing tool targeted at developers and teams.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/devaudit) (version `803.ca0a68e`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S devaudit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/sonatype-nexus-community/DevAudit.git
cd DevAudit
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `mono`
- **System libraries** (Arch name — translate for your distro):
  - `mono-msbuild`
  - `mono-tools`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./build.sh
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[devaudit]] · [[BlackArch Tools]]

