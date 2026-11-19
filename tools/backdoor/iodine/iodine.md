---
name: "iodine"
categories:
  - post-exploitation
version: "0.9.0-1"
homepage: "https://code.kryo.se/iodine"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-10-25"
build-system: other
license: ISC, Zlib
meta-checked: "2026-11-09"
github-repo: "yarrick/iodine"
github-stars: 8023
github-pushed: "2026-11-07"
github-archived: false
github-issues: 9
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 318
gh-activity-checked: "2026-11-09"
---

# iodine

> Tunnel IPv4 through DNS queries — `iodined` server plus `iodine` client create a point-to-point tun device over nameserver traffic.

## What it's for

iodine moves IP packets when only DNS egress is permitted (captive portals, locked-down enterprise networks, some post-exploitation scenarios). The server, `iodined`, runs on a host with a delegated subdomain and answers specially crafted queries; the client, `iodine`, encodes upstream data into query names (Base32/64/128, autodetected) and receives data in responses of the best supported type (NULL/PRIVATE/TXT/SRV/MX/CNAME/A, autoprobed). Both sides authenticate with a shared password (`-P`, first 32 characters) and each gets a virtual tun interface (`dns0` on Linux), so any IP tool — SSH, proxies, full default-route tunneling — runs over it. Expect asymmetry and modest speed (roughly tens of kbit/s upstream through carrier-grade caches, Mbit/s on clean LANs). Server and client must speak the same protocol version, and the tunnel itself is NOT encrypted — run SSH or a VPN inside it. DNS relays can also break NULL/TXT; `-T` forces a different query type, `-m`/`-M` tune fragment and hostname lengths.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Start the tunnel server on a host with a delegated subdomain

After delegating `t1.corp.example.com` NS to the iodined host, bring up the server end with a tunnel IP and password.

```
sudo iodined -f -c -P secretpassword 192.168.99.1 t1.corp.example.com
```

Outcome: iodined opens tun device `dns0` with address 192.168.99.1, listens for DNS queries on UDP 53, and stays in the foreground (`-f`); `-c` also allows clients behind unexpected networks to connect. Log lines show the tunnel ready for a client.

### Connect the client through the local DNS resolver

On the restricted host, bring up the client half using the system's configured resolver.

```
sudo iodine -f -P secretpassword t1.corp.example.com
```

Outcome: after password login and codec/query-type autoprobing, the client's `dns0` gets 192.168.99.2 with a suitable MTU; `ping 192.168.99.1` over the tunnel confirms it works.

### Connect directly to the iodined host and force DNS-mode tunneling

In a lab without any resolver in between (or when you must not fall back to raw UDP), point the client straight at the server and pin DNS mode.

```
sudo iodine -f -r -P secretpassword 198.51.100.10 t1.corp.example.com
```

Outcome: queries go directly to 198.51.100.10 (the iodined host) instead of a relay; `-r` disables iodine's automatic switch to raw-UDP tunneling even though direct port 53 traffic is possible, keeping everything inside DNS packets.

### Verify the delegation before running anything

Confirm the world routes the tunnel domain to your iodined host with a plain DNS probe.

```
dig -t NS foo123.t1.corp.example.com
```

Outcome: a working delegation returns your server's hostname (e.g. `ns.io.citronna.de.`-style answer from iodined); queries prefixed with `z` (any type) return garbled text, proving iodined itself is answering.

### Run iodined when UDP 53 is already taken

Move iodined to an alternate port and redirect the standard one with iptables.

```
sudo iodined -f -P secretpassword -p 5353 192.168.99.1 t1.corp.example.com
sudo iptables -t nat -A PREROUTING -i eth0 -p udp --dport 53 -j DNAT --to :5353
```

Outcome: iodined binds 5353 while the DNAT rule forwards arriving port-53 UDP traffic to it, letting the pre-existing DNS service keep its config and the client connect unchanged.

### Route all client traffic through the DNS tunnel

The README's full-tunneling recipe: keep a host route to the resolver via the real gateway, then point the default route at iodined's tunnel IP and NAT on the server.

```
sudo ip route add 198.51.100.10 via 192.168.0.1 dev eth0
sudo ip route replace default via 192.168.99.1
```

Outcome: all client traffic now flows inside DNS queries; enable NAT/masquerade on the iodined host so tunneled packets reach the internet. Because the tunnel is unencrypted, layer SSH or a VPN over it before sending anything sensitive.
