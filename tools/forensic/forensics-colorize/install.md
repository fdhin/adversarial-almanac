---
tool: "forensics-colorize"
build-system: "other"
upstream: "https://github.com/jessek/colorize"
recipe: "kali-dsc"
version: "1.1-7"
---

# Installing forensics-colorize

> show differences between files using color graphics

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/forensics-colorize) (version `1.1-7`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install forensics-colorize
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss forensics-colorize`); otherwise build from source below.

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/jessek/colorize
cd colorize
# see exact recipe steps below
```

## Build dependencies (from source .dsc)

- `debhelper-compat`
- Runtime deps: see [pkg.kali.org/pkg/forensics-colorize](https://pkg.kali.org/pkg/forensics-colorize)

Backlinks: [[forensic]] · [[forensics-colorize]] · [[Kali Gap Analysis]]
