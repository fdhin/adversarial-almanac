---
tool: "profuzz"
build-system: "scripts"
upstream: "https://github.com/HSASec/ProFuzz"
recipe: "blackarch"
source: "https://github.com/HSASec/ProFuzz"
license: "GPL3"
blackarch-pkgver: "9.aa6dded"
---

# Installing profuzz

> Simple PROFINET fuzzer based on Scapy.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/profuzz) (version `9.aa6dded`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S profuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/HSASec/ProFuzz.git
cd ProFuzz
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `graphviz`
  - `python2`
  - `tcpdump`
- ⚠️ Python 2 only deps: python2-gnuplot, python2-pycryptodome, python2-pyqt4, python2-pyx, python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[profuzz]] · [[BlackArch Tools]]

