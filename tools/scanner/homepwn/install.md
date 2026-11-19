---
tool: "homepwn"
build-system: "scripts"
upstream: "https://github.com/ElevenPaths/HomePWN"
recipe: "blackarch"
source: "https://github.com/ElevenPaths/HomePWN"
license: "GPL3"
blackarch-pkgver: "31.0803981"
---

# Installing homepwn

> Swiss Army Knife for Pentesting of IoT Devices.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/homepwn) (version `31.0803981`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S homepwn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/ElevenPaths/HomePWN.git
cd HomePWN
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `dbus`, `prompt_toolkit`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bluez`
  - `bluez-tools`
  - `cups`
  - `dnsmasq`
  - `glib2`
  - `hostapd`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libxml2` — Debian libxml2-dev / Fedora libxml2-devel
  - `nmap`
  - `pcsc-tools`
  - `python`
  - `screen`
  - `tcpdump`
  - `wireshark-cli`
  - `wondershaper`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[homepwn]] · [[BlackArch Tools]]

