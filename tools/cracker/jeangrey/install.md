---
tool: "jeangrey"
build-system: "scripts"
upstream: "https://github.com/SideChannelMarvels/JeanGrey"
recipe: "blackarch"
source: "https://github.com/SideChannelMarvels/JeanGrey"
license: "GPL3"
blackarch-pkgver: "43.9bf3cf4"
---

# Installing jeangrey

> A tool to perform differential fault analysis attacks (DFA).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jeangrey) (version `43.9bf3cf4`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S jeangrey    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/SideChannelMarvels/JeanGrey.git
cd JeanGrey
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[jeangrey]] · [[BlackArch Tools]]

