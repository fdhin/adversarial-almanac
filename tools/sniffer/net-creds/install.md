---
tool: "net-creds"
build-system: "scripts"
upstream: "https://github.com/DanMcInerney/net-creds"
recipe: "blackarch"
source: "https://github.com/DanMcInerney/net-creds"
license: "GPL3"
blackarch-pkgver: "87.07a25e1"
---

# Installing net-creds

> Sniffs sensitive data from interface or pcap.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/net-creds) (version `87.07a25e1`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S net-creds    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/DanMcInerney/net-creds) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `iproute2`
  - `net-tools`
  - `python2`
  - `scapy`
- ⚠️ Python 2 only deps: python2-scapy, python2-wsgiref (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[net-creds]] · [[BlackArch Tools]]

