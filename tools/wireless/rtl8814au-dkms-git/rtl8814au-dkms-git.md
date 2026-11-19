---
name: "rtl8814au-dkms-git"
categories:
  - blackarch-wireless
version: "5.8.5.1.r207.g1840d7b"
homepage: "https://github.com/morrownr/8814au"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-08-24"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "morrownr/8814au"
github-stars: 638
github-pushed: "2026-08-19"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 17
gh-activity-checked: "2026-11-09"
---

# rtl8814au-dkms-git

> DKMS-packaged Linux driver for Realtek RTL8814AU / RTL8813AU USB Wi-Fi adapters.

## What it's for

This is a kernel driver package, not a userspace program: it builds
the 8814au driver (Realtek firmware v5.8.5.1 branch) against the
running kernel through DKMS, so the module is rebuilt automatically on
kernel updates. It gives high-power 802.11ac USB adapters such as the
ASUS USB-AC68 or Edimax EW-7833UAC a working wireless interface with
managed, monitor and AP interface modes. There is no command of its
own to run — once the module loads, the adapter is driven entirely by
the standard Linux stack (`iw`, `wpa_supplicant`, `hostapd`, capture
tools in monitor mode). The upstream project recommends USB 3.0 ports
for these adapters and documents runtime module options through
`/etc/modprobe.d/8814au.conf`.

## Typical use cases

### Load the driver and confirm the adapter came up

Bring a freshly installed adapter into the system.

```
sudo modprobe 8814au
iw dev
```

Outcome: `iw dev` lists a new wireless interface (e.g. `wlan1`) for
the adapter, and `dmesg | tail` shows the 8814au driver binding to
the USB device. If no interface appears, check that headers matching
the running kernel were available when DKMS built the module.

### Put the adapter into monitor mode

Prepare the interface for capture tools.

```
sudo ip link set wlan1 down
sudo iw dev wlan1 set type monitor
sudo ip link set wlan1 up
sudo iw dev wlan1 set channel 149
```

Outcome: `iw dev` reports `wlan1` as type monitor; hand it to
airodump-ng, tcpdump or Wireshark for raw 802.11 capture. Monitor is
one of the driver's supported interface modes; switch back with
`sudo iw dev wlan1 set type managed`.

### Run the adapter as a software access point

Use AP mode with hostapd, for example to serve a test network.

```
sudo hostapd /etc/hostapd/hostapd.conf
```

Outcome: hostapd (with `driver=nl80211` and `interface=wlan1`) brings
the adapter up as an access point on the configured channel — AP is
listed among the driver's supported interface modes, so stations
associate as with any Wi-Fi interface.

*No corpus evidence — derived from upstream documentation (README: supported interface modes Managed/Monitor/AP, supported hardware list, USB 3.0 and modprobe.d configuration notes).*
