---
tool: "cmseek"
build-system: "scripts"
upstream: "https://github.com/Tuhinshubhra/CMSeeK"
recipe: "blackarch"
source: "https://github.com/Tuhinshubhra/CMSeeK"
license: "GPL3"
blackarch-pkgver: "387.03a6e7f"
---

# Installing cmseek

> CMS (Content Management Systems) Detection and Exploitation suite.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cmseek) (version `387.03a6e7f`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S cmseek    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Tuhinshubhra/CMSeeK.git
cd CMSeeK
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[cmseek]] · [[BlackArch Tools]]

