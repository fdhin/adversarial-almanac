---
name: "faifa"
gentoo-category: "net-misc"
version: "9999"
homepage: "https://github.com/ffainelli/faifa"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: autotools
license: BSD
meta-checked: "2026-11-09"
github-repo: "ffainelli/faifa"
github-stars: 63
github-pushed: "2021-05-11"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# faifa

> Configure and probe HomePlug AV powerline adapters over Ethernet — keys, station info, topology.

*No corpus evidence — derived from upstream documentation.*

## What it's for

faifa (by Florian Fainelli, Xavier Carcelle, and Nicolas Thill) speaks the HomePlug AV/AV2 powerline management protocol — Ethernet frames of Ethertype 0x88e1, originally the Intellon (now Qualcomm Atheros) INT5000/INT6000 chipsets — from a Linux host whose interface is on the same powerline network. It can send vendor-specific and protocol management frames, set the network encryption key, dump received frames with proper parsing, and discover the HPAV network topology (coordinator and stations). It is also installed as the `libfaifa` library for programs that need to send HomePlug frames. Notable quirk: Broadcom-based adapters do not answer the default Intellon destination address, so you must set an explicit destination MAC (the adapter's, or broadcast). It manages powerline devices only, needs an interface attached to the powerline segment, and raw frame access means running it as root.

## Typical use cases

### Open the management menu for a powerline segment

Attach to the interface wired to the powerline adapter and pick an operation.

```
sudo faifa -i eth0 -m
```

Outcome: brings up the menu of known management-message (MM) types on `eth0` — select an operation to send the corresponding HomePlug AV frame and print the device's parsed response.

### Talk to Broadcom-based adapters

Reach adapters that ignore the default Intellon destination address.

```
sudo faifa -i eth0 -a ff:ff:ff:ff:ff:ff -m
```

Outcome: same management menu, but frames are addressed to the broadcast MAC instead of the default Intellon address, which Broadcom HomePlug adapters require before they respond.

### Set the network encryption key

Rekey a logical powerline network (all adapters sharing the NMK form one network).

```
sudo faifa -i eth0 -k Password1
```

Outcome: sends the set-key management frame with the given network key; adapters that adopt it join/rejoin the logical network — also the standard way to segment a building's powerline infrastructure from a neighbor's.

### Verbose frame exchange

Watch the raw protocol dialogue while operating.

```
sudo faifa -i eth0 -v -m
```

Outcome: enables verbose output, printing sent and received management frames with their parsed fields as the session runs — for diagnosing adapters that fail to answer.
