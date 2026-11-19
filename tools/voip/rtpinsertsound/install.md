---
tool: "rtpinsertsound"
build-system: "make"
upstream: "http://www.hackingvoip.com/sec_tools.html"
recipe: "kali-dsc"
version: "3.0-1kali4+b1"
---

# Installing rtpinsertsound

> Inserts audio into a specified stream

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/rtpinsertsound) (version `3.0-1kali4+b1`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install rtpinsertsound
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss rtpinsertsound`); otherwise build from source below.

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.hackingvoip.com/sec_tools.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Build dependencies (from source .dsc)

- `debhelper-compat`, `libfindrtp`, `libnet1-dev`, `libpcap-dev`
- Runtime deps: see [pkg.kali.org/pkg/rtpinsertsound](https://pkg.kali.org/pkg/rtpinsertsound)

Backlinks: [[voip]] · [[rtpinsertsound]] · [[Kali Gap Analysis]]
