---
name: "wifitap"
categories:
  - blackarch-wireless
version: "2b16088"
homepage: "https://github.com/GDSSecurity/wifitap"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "GDSSecurity/wifitap"
github-stars: 153
github-pushed: "2017-03-28"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wifitap

> 2005 research tool that exposes a target Wi-Fi network as a virtual tun/tap interface so ordinary IP tools can talk over raw 802.11 injection.

## What it's for

wifitap (Cedric Blancher, 2005; the GDSSecurity GitHub repository is the surviving mirror) is a proof-of-concept for Wi-Fi communication without association: it binds to a target BSSID on an injection-capable monitor adapter and creates a `wj0` tun/tap interface, injecting and sniffing raw 802.11 frames via Scapy so that any normal IP program — ping, a browser, an ARP spoofer — can operate against the target network as if `wj0` were a regular NIC. It can encrypt/decrypt WEP frames on the fly, and ships with companions that build specific attacks on the same mechanism: `wifiarp.py` (ARP traffic), `wifidns.py` (forged DNS answers), `wifiping.py` (ICMP over injection). This is Python 2 era software built against 2005-era patched Madwifi/Prism drivers: expect it as a historical/educational artifact — WPA2/CCMP networks and modern mac80211 stacks are outside what it was built and last touched for (the mirror's last change is from 2017).

## Typical use cases

### Attach to a target network as a virtual interface

Create the injection-backed `wj0` interface and address it like a NIC.

```
sudo ./wifitap.py -b 00:11:22:33:44:55 -o wlan0 -i wlan0
sudo ifconfig wj0 192.168.1.1 up
```

Outcome: wifitap attaches to the BSSID on the given injection (`-o`) and listening (`-i`) interfaces and creates `wj0`; after configuring it with an address, ordinary tools route through `wj0` and their traffic goes out as injected 802.11 frames toward the target network — `-s 00:11:22:33:44:55` spoofs the source MAC if needed.

### Communicate on a WEP network with on-the-fly encryption

Inject and read WEP-encrypted frames without joining normally.

```
sudo ./wifitap.py -b 00:11:22:33:44:55 -w 01:02:03:04:05 -k 0
```

Outcome: same `wj0` interface, but frames injected through it are WEP-encrypted with the given key (hex, colon-separated or dashed forms accepted) and received frames decrypted using key id `-k` (0-3) — traffic on `wj0` then reads as plaintext.

### Answer DNS queries for associated clients

Redirect name resolution using the DNS companion.

```
sudo ./wifidns.py -b 00:11:22:33:44:55 -a 198.51.100.10
```

Outcome: listens for DNS queries headed to the target network and injects forged replies resolving names to 198.51.100.10, built on the same frame-injection core (`-t` sets the answer TTL); `wifiarp.py -b <BSSID> -s <SMAC>` is the ARP counterpart for poisoning.

*No corpus evidence — derived from upstream documentation (usage and flags verified against the packaged README and each script's usage() text).*
