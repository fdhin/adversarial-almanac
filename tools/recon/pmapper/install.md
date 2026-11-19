---
tool: "pmapper"
build-system: "scripts"
upstream: "https://github.com/nccgroup/PMapper"
recipe: "blackarch"
source: "https://github.com/nccgroup/PMapper"
license: "AGPL"
blackarch-pkgver: "82.91d2e60"
---

# Installing pmapper

> A tool for quickly evaluating IAM permissions in AWS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pmapper) (version `82.91d2e60`, license AGPL).

## Arch / BlackArch

```bash
sudo pacman -S pmapper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/nccgroup/PMapper.git
cd PMapper
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `botocore`, `dateutil`, `packaging`, `pydot`, `tqdm`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[pmapper]] · [[BlackArch Tools]]

