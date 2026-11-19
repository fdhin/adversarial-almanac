---
tool: "nohidy"
build-system: "scripts"
upstream: "https://github.com/flipchan/Nohidy"
recipe: "blackarch"
source: "https://github.com/flipchan/Nohidy"
license: "GPL2"
blackarch-pkgver: "67.22c1283"
---

# Installing nohidy

> The system admins best friend, multi platform auditing tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nohidy) (version `67.22c1283`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S nohidy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/flipchan/Nohidy.git
cd Nohidy
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[nohidy]] · [[BlackArch Tools]]

