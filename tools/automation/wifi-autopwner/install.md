---
tool: "wifi-autopwner"
build-system: "scripts"
upstream: "https://github.com/Mi-Al/WiFi-autopwner"
recipe: "blackarch"
source: "https://github.com/Mi-Al/WiFi-autopwner"
license: "GPL3"
blackarch-pkgver: "36.faa4d01"
---

# Installing wifi-autopwner

> Script to automate searching and auditing Wi-Fi networks with weak security.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wifi-autopwner) (version `36.faa4d01`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S wifi-autopwner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Mi-Al/WiFi-autopwner.git
cd WiFi-autopwner
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `crda`
  - `create_ap`
  - `crunch`
  - `curl`
  - `hashcat`
  - `iw`
  - `nmap`
  - `pixiewps`
  - `reaver`
  - `routersploit`
  - `sipcalc`
  - `wireless-regdb`
  - `wireshark-qt`
  - `wpa_supplicant`
  - `xterm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[wifi-autopwner]] · [[BlackArch Tools]]

