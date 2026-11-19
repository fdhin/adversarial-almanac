---
tool: "wordlister"
build-system: "scripts"
upstream: "https://github.com/4n4nk3/Wordlister"
recipe: "blackarch"
source: "https://github.com/4n4nk3/Wordlister"
license: "GPL3"
blackarch-pkgver: "56.7457c21"
---

# Installing wordlister

> A simple wordlist generator and mangler written in python.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wordlister) (version `56.7457c21`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S wordlister    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/4n4nk3/Wordlister.git
cd Wordlister
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[wordlister]] · [[BlackArch Tools]]

