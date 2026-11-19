---
tool: "wifi-framework"
build-system: "python"
upstream: "https://github.com/domienschepers/wifi-framework"
recipe: "upstream"
license: "BSD"
---

# Installing wifi-framework

> Research framework running Wi-Fi attack/fuzz test cases on top of hostapd and wpa_supplicant.

Source: HackTricks feed admission; upstream [`domienschepers/wifi-framework`](https://github.com/domienschepers/wifi-framework).

## Install

```bash
git clone https://github.com/domienschepers/wifi-framework
cd wifi-framework
./setup.sh
```

`setup.sh` builds patched hostapd/wpa_supplicant daemons and creates the Python
virtual environment at `setup/venv` (inspect the `setup` directory to see every
step it performs).

## Before every usage

```bash
source setup/venv/bin/activate
```

If `sudo` drops the venv, open a root shell first and activate it there.
Disable competing network managers and stale daemons:

```bash
nmcli radio wifi off
killall hostapd wpa_supplicant
```

## Optional: simulated radios

```bash
./setup/setup-hwsim.sh 4      # four mac80211_hwsim interfaces (default: two)
```

Lets every experiment run with no physical Wi-Fi hardware.

## Not yet packaged

No distro packages wifi-framework — clone and `./setup.sh` only. Checked 2026-11-13.

Backlinks: [[wireless]] · [[wifi-framework]]
