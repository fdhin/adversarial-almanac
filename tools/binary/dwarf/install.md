---
tool: "dwarf"
build-system: "scripts"
upstream: "https://github.com/iGio90/Dwarf"
recipe: "blackarch"
source: "https://github.com/iGio90/Dwarf"
license: "GPL3"
blackarch-pkgver: "1084.fd859ae"
---

# Installing dwarf

> Full featured multi arch/os debugger built on top of PyQt5 and frida.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dwarf) (version `1084.fd859ae`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S dwarf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/iGio90/Dwarf.git
cd Dwarf
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `capstone`, `event-bus`, `frida`, `hexdump`, `pyperclip`, `pyqt5`, `qdarkstyle`, `requests`, `unicorn`
- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[dwarf]] · [[BlackArch Tools]]

