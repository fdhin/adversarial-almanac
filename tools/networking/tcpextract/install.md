---
tool: "tcpextract"
build-system: "python"
upstream: "https://pypi.org/project/tcpextract/#files"
recipe: "blackarch"
source: "https://pypi.org/project/tcpextract/#files"
license: "GPL3"
blackarch-pkgver: "1.1"
---

# Installing tcpextract

> Extracts files from captured TCP sessions. Support live streams and pcap files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tcpextract) (version `1.1`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S tcpextract    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.python.org/pypi/tcpextract/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pynids (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[tcpextract]] · [[BlackArch Tools]]

