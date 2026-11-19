---
tool: "leo"
build-system: "scripts"
upstream: "https://github.com/leo-editor/leo-editor/"
recipe: "blackarch"
aur-package: "leo"
source: "https://github.com/leo-editor/leo-editor/"
license: "custom:unknown"
blackarch-pkgver: "40030.ada28bd871"
---

# Installing leo

> Literate programmer's editor, outliner, and project manager.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/leo) (version `40030.ada28bd871`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S leo    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/leo-editor/leo-editor.git
cd leo-editor
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `flask`, `pmw`, `pyqt5`, `setuptools`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `tk`
  - `unzip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[leo]] · [[BlackArch Tools]]

