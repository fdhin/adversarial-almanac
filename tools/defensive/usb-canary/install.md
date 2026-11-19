---
tool: "usb-canary"
build-system: "scripts"
upstream: "https://github.com/errbufferoverfl/usb-canary"
recipe: "blackarch"
source: "https://github.com/errbufferoverfl/usb-canary"
license: "GPL3"
blackarch-pkgver: "31.bb23552"
---

# Installing usb-canary

> A Linux or OSX tool that uses psutil to monitor devices while your computer is locked. In the case it detects someone plugging in or unplugging devices it can be configured to send you an SMS or alert you via Slack or Pushover.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/usb-canary) (version `31.bb23552`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S usb-canary    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/errbufferoverfl/usb-canary) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pip, python2-virtualenv (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[usb-canary]] · [[BlackArch Tools]]

