---
tool: "nexmon"
build-system: "make"
upstream: "https://github.com/seemoo-lab/nexmon"
recipe: "upstream"
license: "see repository"
---

# Installing nexmon

> Broadcom/Cypress Wi-Fi firmware patching framework: monitor mode with radiotap headers and frame injection on Android devices, Raspberry Pis and routers.

Source: HackTricks feed admission; upstream [`seemoo-lab/nexmon`](https://github.com/seemoo-lab/nexmon).

## WARNING

Patching may damage hardware and void warranties. The chip firmware is
replaced — always run `make backup-firmware` before installing.

## Build prerequisites (x86_64 Linux host)

```bash
sudo apt-get install git gawk qpdf adb flex bison
sudo dpkg --add-architecture i386
sudo apt-get update && sudo apt-get install libc6:i386 libncurses5:i386 libstdc++6:i386
```

## Build and install (Android target)

```bash
git clone https://github.com/seemoo-lab/nexmon.git
cd nexmon && source setup_env.sh && make
cd patches/<chip>/<fw_version>/nexmon/
make                # compile patched firmware
make backup-firmware
make install-firmware   # device attached via adb
```

Utilities (`nexutil`, `libfakeioctl`) build with the **Android NDK (r11c)** —
export `NDK_ROOT` and run `make` in `utilities/`; the nexmon app
(<https://nexmon.org/app>) bundles prebuilt ones.

## Raspberry Pi

```bash
sudo apt install raspberrypi-kernel-headers git libgmp3-dev gawk qpdf bison flex make autoconf libtool texinfo
```

Same nexmon root build, then `cd patches/bcm43455c0/<fw>/nexmon/`, `make`,
`make backup-firmware`, `make install-firmware`, and install `nexutil` from
`utilities/nexutil/` (`make && make install`). Kernel support: 4.4–5.15
depending on firmware version. Recent Pixel devices install via a Magisk
module instead (see the supported-devices table upstream).

## Not yet packaged

No distro packages nexmon — device-specific firmware builds only. Checked 2026-11-13.

Backlinks: [[firmware]] · [[nexmon]]
