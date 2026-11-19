---
tool: "shellpop"
build-system: "scripts"
upstream: "https://github.com/0x00-0x00/ShellPop"
recipe: "blackarch"
source: "https://github.com/0x00-0x00/ShellPop"
license: "MIT"
blackarch-pkgver: "148.a145349"
---

# Installing shellpop

> Generate easy and sophisticated reverse or bind shell commands.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shellpop) (version `148.a145349`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S shellpop    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/0x00-0x00/ShellPop.git
cd ShellPop
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argcomplete, python2-netifaces, python2-pyperclip, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[shellpop]] · [[BlackArch Tools]]

