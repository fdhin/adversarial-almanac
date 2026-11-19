---
tool: "chameleonmini"
build-system: "make"
upstream: "https://github.com/emsec/ChameleonMini"
recipe: "blackarch"
source: "https://github.com/emsec/ChameleonMini"
license: "custom:unknown"
blackarch-pkgver: "613.ad0eece"
provides: ["chamlog", "chamtool"]
---

# Installing chameleonmini

> Official repository of ChameleonMini, a freely programmable, portable tool for NFC security analysis that can emulate and clone contactless cards, read RFID tags and sniff/log RF data.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/chameleonmini) (version `613.ad0eece`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S chameleonmini    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/emsec/ChameleonMini.git
cd ChameleonMini
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `pyserial`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `avr-gcc`
  - `avr-libc`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  # build firmware
  cd Firmware/Chameleon-Mini

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/chamlog`, `/usr/bin/chamtool`

Backlinks: [[social]] · [[chameleonmini]] · [[BlackArch Tools]]

