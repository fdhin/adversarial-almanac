---
tool: "weirdaal"
build-system: "scripts"
upstream: "https://github.com/carnal0wnage/weirdAAL"
recipe: "blackarch"
source: "https://github.com/carnal0wnage/weirdAAL"
license: "custom:unknown"
blackarch-pkgver: "331.c14e36d"
---

# Installing weirdaal

> AWS Attack Library.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/weirdaal) (version `331.c14e36d`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S weirdaal    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/carnal0wnage/weirdAAL.git
cd weirdAAL
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `boto3`, `botocore`, `dateutil`, `docutils`, `jmespath`, `s3transfer`, `six`, `tabulate`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[weirdaal]] · [[BlackArch Tools]]

