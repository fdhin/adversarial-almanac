---
tool: "tcpwatch"
build-system: "python"
upstream: "http://hathawaymix.org/Software/TCPWatch"
recipe: "blackarch"
source: "http://hathawaymix.org/Software/TCPWatch"
license: "ZPL"
blackarch-pkgver: "1.3.1"
---

# Installing tcpwatch

> A utility written in Python that lets you monitor forwarded TCP connections or HTTP proxy connections.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tcpwatch) (version `1.3.1`, license ZPL).

## Arch / BlackArch

```bash
sudo pacman -S tcpwatch    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://hathawaymix.org/Software/TCPWatch) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[tcpwatch]] · [[BlackArch Tools]]

