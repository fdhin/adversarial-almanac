---
tool: "rtp-flood"
build-system: "make"
upstream: "http://www.hackingexposedvoip.com/"
recipe: "blackarch"
source: "http://www.hackingexposedvoip.com/tools/rtpflood.tar.gz"
license: "GPL2"
blackarch-pkgver: "1.0"
---

# Installing rtp-flood

> RTP flooder

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rtp-flood) (version `1.0`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S rtp-flood    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
curl -fLO http://www.hackingexposedvoip.com/tools/rtpflood.tar.gz
tar xf rtpflood.tar.gz
cd rtpflood
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[voip]] · [[rtp-flood]] · [[BlackArch Tools]]

