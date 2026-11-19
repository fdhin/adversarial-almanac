---
name: "hubbit-sniffer"
categories:
  - blackarch-sniffer
version: "74.460ecf8"
homepage: "https://github.com/cthit/hubbIT-sniffer"
packaged-by: [blackarch]
blackarch-builddate: "2018-08-06"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "cthit/hubbIT-sniffer"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# hubbit-sniffer

> Monitor-mode WiFi sniffer that reports sender MAC addresses to the hubbIT presence-tracking REST API.

## What it's for

hubbIT is a member-presence tracking system run by the IT committee of the Chalmers student union; this sniffer is its data feed. It is a Python 3 script that runs tshark on a monitor-mode wireless interface, records the MAC address of every WiFi frame sender in range, and periodically POSTs the collected addresses as a batch (default every 5 seconds) to a hubbIT REST API endpoint authorized by an API key. A newline-separated blacklist file lets you exclude addresses — most importantly nearby access points, whose frames would otherwise dominate. It requires tshark, python-requests, and a wireless card supporting monitor and promiscuous mode (membership in the wireshark group lets it run without root). The upstream repository is gone; this note follows the archived README.

## Typical use cases

### Prepare the monitor-mode interface

```
sudo /usr/share/hubbit-sniffer/scripts/setup-hubbit-iface.sh
```

Outcome: puts the wireless interface into the monitor/promiscuous configuration the sniffer expects.

### Start reporting seen MAC addresses

```
sudo hubbit-sniffer -a API_KEY -b blacklist.txt
```

Outcome: tshark captures WiFi frames on the monitor interface and the sender MACs are batched and posted to the hubbIT API every 5 seconds under API_KEY; addresses listed in `blacklist.txt` are ignored. Presence appears in hubbIT as members' devices come into range.

### Ignore traffic from nearby access points

```
/usr/share/hubbit-sniffer/scripts/nearby-aps.sh > /usr/share/hubbit-sniffer/sniffer/blacklist.txt
```

Outcome: writes the MAC addresses of access points visible nearby into the blacklist file so their frames are never reported.

### Tune the reporting interval

```
sudo hubbit-sniffer -a API_KEY -t 2
```

Outcome: batch updates are sent to the server every 2 seconds instead of the default 5, trading API load for fresher presence data.

*No corpus evidence — derived from upstream documentation.*
