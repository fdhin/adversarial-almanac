---
tool: "sigdigger"
build-system: "other"
upstream: "https://github.com/BatchDrake/SigDigger"
recipe: "upstream"
license: "LGPL3"
---

# Installing SigDigger

> Qt digital signal analyzer over Suscan/Sigutils — SDR signal reverse engineering (GUI).

Source: hacktricks feed; upstream [`BatchDrake/SigDigger`](https://github.com/BatchDrake/SigDigger).

## AppImage (GNU/Linux, fastest start)

Grab the latest self-contained development build from the [releases](https://github.com/BatchDrake/SigDigger/releases/tag/latest) `latest` tag — no dependencies to install, just make it executable and run.

## One-shot build script (GNU/Linux)

```bash
wget https://actinid.org/blsd
chmod a+x blsd
./blsd
```

Builds SigDigger plus its dependency stack inside a directory under the current working directory (nothing installed system-wide); pass plugin repo names as arguments to bundle them (e.g. the AmateurDSN, APT, AntSDR or ZeroMQ plugins).

## Build from source (Unix)

SigDigger wraps three libraries that must be built and installed first — in order:

```bash
git clone --recursive https://github.com/BatchDrake/sigutils && cd sigutils && mkdir -p build && cd build && cmake .. && make && sudo make install && cd ../..
git clone --recursive https://github.com/BatchDrake/suscan   && cd suscan   && mkdir -p build && cd build && cmake .. && make && sudo make install && cd ../..
git clone https://github.com/BatchDrake/SuWidgets            && cd SuWidgets && qmake6 SuWidgetsLib.pro && make && sudo make install && cd ..
git clone https://github.com/BatchDrake/SigDigger            && cd SigDigger && qmake6 SigDigger.pro    && make && sudo make install && cd ..
```

Then launch with `SigDigger` (or `/opt/SigDigger/bin/SigDigger` if it installed there). Requires Qt5 >= 5.9 or Qt6 development frameworks.

## Notes

- All four projects keep the latest working changes on `master` (no `develop` clones needed anymore).
- macOS: ask upstream for a custom `.dmg`; Windows: no prebuilt support — build from source via the User's Manual (`doc/SigDigger_User_Manual.pdf`).
- SDR access goes through SoapySDR — install the driver module for your device (RTL-SDR, HackRF, LimeSDR, ...).

Backlinks: [[radio]] · [[sigdigger]]
