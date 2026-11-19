---
tool: "driftnet"
build-system: "make"
upstream: "https://github.com/deiv/driftnet"
recipe: "kali-dsc"
nix-package: "driftnet"
version: "1.5.0-0.3"
---

# Installing driftnet

> picks out and displays images from network traffic

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/driftnet) (version `1.5.0-0.3`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install driftnet
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss driftnet`); otherwise build from source below.

## Nix (works on any distro)

```bash
nix run nixpkgs#driftnet    # ad-hoc run, no install
nix profile install nixpkgs#driftnet   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/deiv/driftnet
cd driftnet
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Build dependencies (from source .dsc)

- `debhelper-compat`, `libcmocka-dev`, `libgif-dev`, `libgtk-3-dev`, `libjpeg-dev`, `libpcap-dev`, `libpng-dev`, `libwebsockets-dev`, `libwebp-dev`
- Runtime deps: see [pkg.kali.org/pkg/driftnet](https://pkg.kali.org/pkg/driftnet)

Backlinks: [[sniffer]] · [[driftnet]] · [[Kali Gap Analysis]]
