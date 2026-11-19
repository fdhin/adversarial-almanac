---
tool: "massexpconsole"
build-system: "scripts"
upstream: "https://github.com/jm33-m0/massExpConsole"
recipe: "blackarch"
source: "https://github.com/jm33-m0/massExpConsole"
license: "GPL3"
blackarch-pkgver: "v2.3.5.r1.g530c880"
---

# Installing massexpconsole

> A collection of tools and exploits with a cli ui for mass exploitation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/massexpconsole) (version `v2.3.5.r1.g530c880`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S massexpconsole    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/jm33-m0/mec.git
cd mec
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `docopt`, `html5lib`, `prompt_toolkit`, `psutil`, `pyopenssl`, `requests`, `tqdm`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[massexpconsole]] · [[BlackArch Tools]]

