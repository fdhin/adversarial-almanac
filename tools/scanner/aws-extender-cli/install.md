---
tool: "aws-extender-cli"
build-system: "scripts"
upstream: "https://github.com/VirtueSecurity/aws-extender-cli"
recipe: "blackarch"
source: "https://github.com/VirtueSecurity/aws-extender-cli"
license: "MIT"
blackarch-pkgver: "17.a351154"
---

# Installing aws-extender-cli

> Script to test S3 buckets as well as Google Storage buckets and Azure Storage containers for common misconfiguration issues.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/aws-extender-cli) (version `17.a351154`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S aws-extender-cli    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/VirtueSecurity/aws-extender-cli) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `virtualenv`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[aws-extender-cli]] · [[BlackArch Tools]]

