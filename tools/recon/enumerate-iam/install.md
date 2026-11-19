---
tool: "enumerate-iam"
build-system: "scripts"
upstream: "https://github.com/andresriancho/enumerate-iam"
recipe: "blackarch"
source: "https://github.com/andresriancho/enumerate-iam"
license: "GPL3"
blackarch-pkgver: "14.4529114"
---

# Installing enumerate-iam

> Enumerate the permissions associated with an AWS credential set.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/enumerate-iam) (version `14.4529114`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S enumerate-iam    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/andresriancho/enumerate-iam) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `boto3`, `botocore`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[enumerate-iam]] · [[BlackArch Tools]]

