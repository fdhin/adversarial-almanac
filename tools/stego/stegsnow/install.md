---
tool: "stegsnow"
build-system: "other"
upstream: "https://www.darkside.com.au/snow"
recipe: "kali-dsc"
aur-package: "stegsnow"
version: "20130616-8"
---

# Installing stegsnow

> steganography using ASCII files

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/stegsnow) (version `20130616-8`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install stegsnow
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss stegsnow`); otherwise build from source below.

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.darkside.com.au/snow) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Build dependencies (from source .dsc)

- `debhelper-compat`
- Runtime deps: see [pkg.kali.org/pkg/stegsnow](https://pkg.kali.org/pkg/stegsnow)

Backlinks: [[stego]] · [[stegsnow]] · [[Kali Gap Analysis]]
