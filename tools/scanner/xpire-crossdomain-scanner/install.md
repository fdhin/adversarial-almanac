---
tool: "xpire-crossdomain-scanner"
build-system: "scripts"
upstream: "https://github.com/mandatoryprogrammer/xpire-crossdomain-scanner"
recipe: "blackarch"
source: "https://github.com/mandatoryprogrammer/xpire-crossdomain-scanner.git"
license: "GPL2"
blackarch-pkgver: "1.0cb8d3b"
---

# Installing xpire-crossdomain-scanner

> Scans crossdomain.xml policies for expired domain names.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xpire-crossdomain-scanner) (version `1.0cb8d3b`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S xpire-crossdomain-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/mandatoryprogrammer/xpire-crossdomain-scanner.git
cd xpire-crossdomain-scanner
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-defusedxml, python2-netaddr, python2-python-whois, python2-requests, python2-tldextract (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[xpire-crossdomain-scanner]] · [[BlackArch Tools]]

