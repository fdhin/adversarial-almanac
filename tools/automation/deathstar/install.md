---
tool: "deathstar"
build-system: "scripts"
upstream: "https://github.com/byt3bl33d3r/DeathStar"
recipe: "blackarch"
source: "https://github.com/byt3bl33d3r/DeathStar"
license: "GPL3"
blackarch-pkgver: "60.d7bcbfd"
---

# Installing deathstar

> Automate getting Domain Admin using Empire.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/deathstar) (version `60.d7bcbfd`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S deathstar    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/byt3bl33d3r/DeathStar.git
cd DeathStar
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `certifi`, `colorama`, `commonmark`, `h11`, `httpcore`, `httpx`, `idna`, `pygments`, `rfc3986`, `rich`, `sniffio`, `typing_extensions`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[deathstar]] · [[BlackArch Tools]]

