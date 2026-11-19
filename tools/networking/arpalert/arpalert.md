---
name: "arpalert"
categories:
  - blackarch-networking
version: "2.0.12"
homepage: "http://www.arpalert.org/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2015-03-21"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "thierry-f-78/arpalert"
github-stars: 2
github-pushed: "2019-05-26"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# arpalert

> ARP watcher that learns your LAN's MAC/IP pairs and fires a script when something new, moved or blacklisted appears.

## What it's for

ARPAlert (by Thierry Fournier) monitors ARP traffic on the local network to detect unauthorized connections. It builds a lease table of every MAC/IP pair it observes, compares it against a whitelist of authorized MACs (`maclist file` in the config), a blacklist (`maclist alert file`) and a DHCP leases file, and raises alerts on new addresses, IP changes behind a known MAC, blacklisted MACs and unauthorized ARP requests. Each alert invokes a script (config `action on detect` or `-e`) with `mac_address ip_address alert_type`, where the type is 0 = IP change, 1 = already-referenced address, 2 = blacklisted address, 3 = unreferenced address, 4 = unauthorized request. It is a detector and script dispatcher — it does not block traffic itself — and needs root to capture.

## Typical use cases

### Learn the LAN in daemon mode

```
sudo arpalert -i eth0 -d -f /etc/arpalert/arpalert.conf
```

Outcome: runs as a detached daemon sniffing `eth0`, building the MAC/IP lease table (dumped to the leases file so learning survives restarts) and logging alerts per the config; `-i all` watches every interface.

### Trigger a response script on detection

```
sudo arpalert -i eth0 -e /usr/local/bin/arp-notify.sh -l /var/lib/arpalert/leases.db
```

Outcome: on every alert event the script runs with the MAC, IP and alert type as arguments — enough to page someone, log to a SIEM, or drive an active response from your own script — while the leases file preserves learned pairs across restarts.

### Check the effective configuration

```
arpalert -v -f /etc/arpalert/arpalert.conf
```

Outcome: dumps every option — config-file values merged with defaults — exactly as the daemon would use them, before committing to daemon mode; add `-w` while testing to also print a dump of the captured ARP packets.

*No corpus evidence — derived from upstream documentation.*
