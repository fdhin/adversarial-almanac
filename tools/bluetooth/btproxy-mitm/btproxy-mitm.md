---
name: "btproxy-mitm"
categories:
  - blackarch-bluetooth
version: "71.cd1c906"
homepage: "https://github.com/conorpp/btproxy"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: other
license: GPL3
aliases: ["btproxy-git"]
meta-checked: "2026-11-09"
github-repo: "conorpp/btproxy"
github-stars: 544
github-pushed: "2020-02-24"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# btproxy-mitm

> Man-in-the-middle proxy for classic Bluetooth connections, with live Python callbacks for inspecting and rewriting traffic.

## What it's for

Inserting a Linux box between two paired classic Bluetooth (BR/EDR) devices — typically a phone (master) and a peripheral such as a smartwatch, headphones, a keyboard, or an OBD-II dongle (slave) — to observe and modify their traffic. It clones the name, class, and address of the targets onto local adapters, connects to the slave, then listens for the master to connect to the clone; once both sides are attached it relays every service socket and prints traffic to stdout. Traffic can be rewritten at runtime through a Python script supplying `master_cb`/`slave_cb` functions. It does not support Bluetooth Low Energy, handles SPP and legacy-PIN pairing only, and targets a BlueZ 4 stack (it hangs on BlueZ 5 for many adapters). The packaged binary is `btproxy-mitm`; upstream calls it `btproxy`. One or two local Bluetooth adapters are required; with two adapters both devices are cloned, which defeats address/name pinning on restrictive implementations.

## Typical use cases

### Proxy a connection between two devices

```
sudo btproxy-mitm AA:BB:CC:DD:EE:01 11:22:33:44:55:66
```

Outcome: takes the master address first, slave second; clones both devices onto the local adapters (cloned names get an `_btproxy` suffix), connects to the slave's services, and waits for the real master to attach. Once the proxy path is complete, relayed traffic streams to the terminal. If a previous pairing with the same addresses exists it is reused automatically — pass `-n`/`--repair` to force a fresh pairing.

### Proxy with a single adapter

```
sudo btproxy-mitm -i hci0 AA:BB:CC:DD:EE:01 11:22:33:44:55:66
```

Outcome: same mitm flow but only the slave is cloned onto `hci0`; useful when a second dongle is unavailable, at the cost of failing against devices that pin the master's address (add `-c` to copy target addresses onto the adapter, which not all adapters support).

### Rewrite traffic in flight

```
sudo btproxy-mitm -s replace.py AA:BB:CC:DD:EE:01 11:22:33:44:55:66
```

Outcome: loads `replace.py`, whose `master_cb(req)` and `slave_cb(res)` functions receive every packet in each direction; whatever they return is forwarded, so packets can be logged, fuzzed, or substituted. Typing `r` at the program console reloads the script without reconnecting, so changes apply to the live session.

### Spoof an adapter address before pairing

```
sudo btproxy-mitm -i hci0 -a AA:BB:CC:DD:EE:01
```

Outcome: sets the BD address of `hci0` to the given value and exits — handy for preparing a dongle to impersonate a specific device before running the proxy.

*No corpus evidence — derived from upstream documentation.*
