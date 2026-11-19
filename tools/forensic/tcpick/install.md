---
tool: "tcpick"
build-system: "make"
upstream: "http://tcpick.sourceforge.net"
recipe: "kali-dsc"
aur-package: "tcpick"
version: "0.2.1-12"
---

# Installing tcpick

> TCP stream sniffer and connection tracker

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/tcpick) (version `0.2.1-12`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install tcpick
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss tcpick`); otherwise build from source below.

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://tcpick.sourceforge.net) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Build dependencies (from source .dsc)

- `debhelper-compat`, `libpcap0.8-dev`
- Runtime deps: see [pkg.kali.org/pkg/tcpick](https://pkg.kali.org/pkg/tcpick)

Backlinks: [[forensic]] · [[tcpick]] · [[Kali Gap Analysis]]
