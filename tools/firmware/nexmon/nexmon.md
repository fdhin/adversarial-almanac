---
name: "nexmon"
categories:
  - firmware
homepage: "https://github.com/seemoo-lab/nexmon"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: make
meta-checked: "2026-11-13"
github-repo: "seemoo-lab/nexmon"
github-stars: 2918
github-archived: false
github-match: "exact"
gh-checked: "2026-11-13"
worth: 6.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "see repository"
---

# nexmon

> C-based firmware patching framework for Broadcom/Cypress Wi-Fi chips: builds patched firmwares that add monitor mode with radiotap headers and frame injection to phones, Raspberry Pis and routers.

## What it's for

Nexmon patches the proprietary firmware of Broadcom/Cypress Wi-Fi chipsets (bcm4339 in the Nexus 5, bcm43455c0 in Raspberry Pi 3B+/4, bcm4389 in Pixel 7, up to the bcm4390 in the Pixel 9, plus routers) to enable features the stock firmware disables — most importantly monitor mode with radiotap headers and arbitrary frame injection, the prerequisites for airodump-ng, mdk4-style attacks and raw 802.11 tooling on otherwise locked-down hardware. The framework itself (build toolchain, ucode patches, flash patches) also underpins sibling research projects for jamming, CSI extraction and using the chip as an SDR. It can damage hardware and void warranties; installation replaces the chip's firmware at runtime or persistently, and on recent Pixel devices it ships as a Magisk module. It does not provide ready-made attack tooling — it unlocks the radio interface that standard Wi-Fi tools then use.

## Typical use cases

### Build a patched firmware for an Android phone

```
git clone https://github.com/seemoo-lab/nexmon.git
cd nexmon
source setup_env.sh
make
cd patches/bcm4339/6_37_34_43/nexmon/
make
make backup-firmware
make install-firmware
```

Outcome: the build environment extracts ucode and flash patches from the original firmware, compiles the patch set for the bcm4339 (Nexus 5 example), `backup-firmware` saves the stock blob on the device, and `install-firmware` pushes the patched firmware over adb — after which `nexutil -m2` enables monitor mode in the chip.

### Enable monitor mode on a rooted phone

```
adb shell
nexutil -m2
LD_PRELOAD=libfakeioctl.so tcpdump -i wlan0
```

Outcome: the firmware enters monitor mode without airmon-ng; `libfakeioctl` is preloaded so tools expecting a radiotap interface see one on `wlan0`, and tcpdump (or airodump-ng) captures 802.11 frames directly. `nexutil -m0` returns to managed mode to associate to an access point again.

### Monitor mode on a Raspberry Pi

```
iw phy `iw dev wlan0 info | gawk '/wiphy/ {printf "phy" $2}'` interface add mon0 type monitor
ifconfig mon0 up
tcpdump -i mon0
```

Outcome: after flashing the nexmon firmware and modified brcmfmac driver for the Pi's bcm43455c0, a native monitor interface `mon0` is created — radiotap headers are already set, so tcpdump/airodump-ng work out of the box, in parallel with normal wlan0 operation.

### Control the firmware over UDP without root

```
ifconfig wlan0 192.168.222.1 netmask 255.255.255.0
nexutil -x<cookie>
nexutil -X<cookie> -m1
```

Outcome: a security cookie is registered once as root; afterwards `-X<cookie>` commands reach the firmware's UDP interface from unprivileged processes (activate monitor mode with `-m1` here) — useful when the capturing tool runs as a non-root user.

### Dump the chip's ROM for analysis

```
make dump-rom
```

Outcome: inside a rom_extraction patch project (bcm4339/bcm4358), builds and flashes a patch that copies the chip's ROM into RAM and dumps it to `rom.bin`, giving the full firmware image (ROM + RAM parts) for static analysis.

*No corpus evidence — derived from upstream documentation.*
