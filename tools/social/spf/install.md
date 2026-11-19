---
tool: "spf"
build-system: "scripts"
upstream: "https://github.com/tatanus/SPF"
recipe: "blackarch"
source: "https://github.com/tatanus/SPF"
license: "custom:unknown"
blackarch-pkgver: "85.344ac2f"
---

# Installing spf

> A python tool designed to allow for quick recon and deployment of simple social engineering phishing exercises.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/spf) (version `85.344ac2f`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S spf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/tatanus/SPF.git
cd SPF
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `dnspython`, `twisted`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `phantomjs`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[spf]] · [[BlackArch Tools]]

