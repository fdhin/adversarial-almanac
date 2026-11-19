---
tool: "ssh-user-enum"
build-system: "scripts"
upstream: "https://github.com/nccgroup/ssh_user_enum"
recipe: "blackarch"
source: "https://github.com/nccgroup/ssh_user_enum"
license: "GPL3"
blackarch-pkgver: "7.ae453c1"
---

# Installing ssh-user-enum

> SSH User Enumeration Script in Python Using The Timing Attack.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ssh-user-enum) (version `7.ae453c1`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S ssh-user-enum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/nccgroup/ssh_user_enum.git
cd ssh_user_enum
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-paramiko (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[ssh-user-enum]] · [[BlackArch Tools]]

