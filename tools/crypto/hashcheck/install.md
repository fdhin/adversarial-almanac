---
tool: "hashcheck"
build-system: "scripts"
upstream: "https://github.com/Telefonica/HashCheck"
recipe: "blackarch"
source: "https://github.com/Telefonica/HashCheck"
license: "custom:unknown"
blackarch-pkgver: "2.72b0c6e"
---

# Installing hashcheck

> Search for leaked passwords while maintaining a high level of privacy using the k-anonymity method.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hashcheck) (version `2.72b0c6e`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S hashcheck    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Telefonica/HashCheck.git
cd HashCheck
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[hashcheck]] · [[BlackArch Tools]]

