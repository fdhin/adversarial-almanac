---
tool: "honeycreds"
build-system: "scripts"
upstream: "https://github.com/Ben0xA/HoneyCreds"
recipe: "blackarch"
source: "https://github.com/Ben0xA/HoneyCreds"
license: "GPL3"
blackarch-pkgver: "26.eaeb401"
---

# Installing honeycreds

> Network credential injection to detect responder and other network poisoners.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/honeycreds) (version `26.eaeb401`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S honeycreds    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Ben0xA/HoneyCreds
cd HoneyCreds
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `cffi`, `smbprotocol`, `splunk-sdk`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[honeycreds]] · [[BlackArch Tools]]

