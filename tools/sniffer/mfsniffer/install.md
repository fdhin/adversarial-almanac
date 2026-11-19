---
tool: "mfsniffer"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/120802/MF-Sniffer-TN3270-Password-Grabber.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/sniffers/MFSniff.zip"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing mfsniffer

> A python script for capturing unencrypted TSO login credentials.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mfsniffer) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S mfsniffer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO https://dl.packetstormsecurity.net/sniffers/MFSniff.zip
tar xf MFSniff.zip
cd MFSniff
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `unzip`
- ⚠️ Python 2 only deps: python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/mfsniffer`

Backlinks: [[sniffer]] · [[mfsniffer]] · [[BlackArch Tools]]

