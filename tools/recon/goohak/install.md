---
tool: "goohak"
build-system: "scripts"
upstream: "https://github.com/1N3/Goohak"
recipe: "blackarch"
source: "https://github.com/1N3/Goohak"
license: "custom:unknown"
blackarch-pkgver: "31.815a31e"
---

# Installing goohak

> Automatically Launch Google Hacking Queries Against A Target Domain.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/goohak) (version `31.815a31e`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S goohak    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/1N3/Goohak.git
cd Goohak
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[goohak]] · [[BlackArch Tools]]

