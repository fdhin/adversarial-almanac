---
tool: "btcrack"
build-system: "scripts"
upstream: "http://www.nruns.com/_en/security_tools_btcrack.php"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/wireless/BTCrack.zip"
license: "custom:unknown"
blackarch-pkgver: "1.1"
---

# Installing btcrack

> The world's first Bluetooth Pass phrase (PIN) bruteforce tool. Bruteforces the Passkey and the Link key from captured Pairing exchanges.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/btcrack) (version `1.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S btcrack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO https://dl.packetstormsecurity.net/wireless/BTCrack.zip
tar xf BTCrack.zip
cd BTCrack
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[bluetooth]] · [[btcrack]] · [[BlackArch Tools]]

