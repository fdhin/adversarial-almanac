---
tool: "pst-utils"
build-system: "autotools"
upstream: "https://www.five-ten-sg.com/libpst/"
recipe: "kali-dsc"
version: "0.6.76-1.3+b1"
---

# Installing pst-utils

> tools for reading Microsoft Outlook PST files

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/pst-utils) (version `0.6.76-1.3+b1`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install pst-utils
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss pst-utils`); otherwise build from source below.

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://www.five-ten-sg.com/libpst/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Build dependencies (from source .dsc)

- `dpkg-dev`, `autoconf-archive`, `debhelper-compat`, `imagemagick`, `libgd-dev`, `libgsf-1-dev`
- Runtime deps: see [pkg.kali.org/pkg/pst-utils](https://pkg.kali.org/pkg/pst-utils)

Backlinks: [[forensic]] · [[pst-utils]] · [[Kali Gap Analysis]]
