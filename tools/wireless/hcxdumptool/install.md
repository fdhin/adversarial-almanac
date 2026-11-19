---
tool: "hcxdumptool"
build-system: "make"
upstream: "https://github.com/ZerBea/hcxdumptool"
recipe: "upstream"
license: "MIT"
---

# Installing hcxdumptool

> WLAN packet capture and WPA layer-2 attack tool (PMKID/handshake harvesting) feeding hcxtools and hashcat.

Source: pentest-book feed; upstream [`ZerBea/hcxdumptool`](https://github.com/ZerBea/hcxdumptool).

## Arch Linux

```bash
sudo pacman -S hcxdumptool
```

Upstream warns distro packages are usually old — prefer building when a current version matters (keep hcxdumptool and hcxpcapngtool versions in sync).

## Nix (works on any distro)

```bash
nix run nixpkgs#hcxdumptool    # ad-hoc run, no install
```

## Build from source (upstream-recommended)

```bash
git clone https://github.com/ZerBea/hcxdumptool.git
cd hcxdumptool
make -j $(nproc)
sudo make install                  # installs to /usr/bin
# or: sudo make install PREFIX=/usr/local
```

Headless tip from upstream: remove `-DSTATUSOUT` from the Makefile before compiling to drop the status display and save CPU on Raspberry-Pi-class boards.

## Dependencies (per README)

- `gcc`, `make`
- `libpcap` and `libpcap-dev` (needed for the internal BPF compiler, used by the `--bpfc=` filter workflow)
- Linux kernel >= 5.15 (latest longterm/stable recommended)
- A WLAN adapter whose driver supports monitor mode *and* full frame injection (Realtek rtl8xxxu/rtw88, Atheros ath9k_htc, Ralink rt2800usb are known-good; Intel/Broadcom/Qualcomm adapters are problematic)

## Notes

- Must run as root; unsupported on Windows, macOS, Android, emulators and wrappers.
- Companion conversion/cracking tools live in [hcxtools](https://github.com/ZerBea/hcxtools) (`hcxpcapngtool`, `hcxhashtool`, ...).
- For 5/6/7 GHz injection, uncomment a suitable regulatory domain in `/etc/conf.d/wireless-regdom`.

Backlinks: [[wireless]] · [[hcxdumptool]]
