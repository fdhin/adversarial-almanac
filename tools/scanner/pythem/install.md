---
tool: "pythem"
build-system: "scripts"
upstream: "https://github.com/m4n3dw0lf/PytheM"
recipe: "blackarch"
source: "https://github.com/m4n3dw0lf/PytheM"
license: "GPL3"
blackarch-pkgver: "454.e4fcb8a"
---

# Installing pythem

> Python2 penetration testing framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pythem) (version `454.e4fcb8a`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pythem    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/m4n3dw0lf/PytheM.git
cd PytheM
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `espeak-ng`
  - `python2`
- ⚠️ Python 2 only deps: python2-mechanize, python2-netaddr, python2-netfilterqueue, python2-paramiko, python2-praw, python2-psutil, python2-pygeoip, python2-pysqlite, python2-pyttsx, python2-ropper, python2-scapy, python2-setuptools, python2-speechrecognition, python2-termcolor (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[pythem]] · [[BlackArch Tools]]

