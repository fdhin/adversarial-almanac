---
name: "multitun"
categories:
  - blackarch-tunnel
version: "1.319a134"
homepage: "https://github.com/covertcodes/multitun"
packaged-by: [blackarch]
blackarch-builddate: "2022-03-27"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "covertcodes/multitun"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# multitun

> "Tunnel all the things" over a WebSocket by covertcodes: IPv4 packets relayed through an RFC 6455 WebSocket that looks like ordinary web traffic, with password-authenticated clients forming a covert VPN.

## What it's for

Whole-host tunneling through firewalls that allow web and HTML5 but hunt for conventional tunnels. A Python 2 / Twisted server terminates WebSocket connections on a web port (default location `mt`) and relays them onto a TUN interface; clients dial the WebSocket, get their own TUN device, and route IP through it. Firewalls that tolerate WebSockets see what looks like browsing — including to deep-packet inspection tuned for SSH-over-80 style tunnels. Users authenticate with per-TUN-IP passwords from the config; multiple clients can share the tunnel network and reach each other (a covert VPN); and the server doubles as a plain web server for non-WebSocket visitors (`webdir`). Payloads are AES-encrypted, but the README still recommends running SSH inside the tunnel for anything serious. The original GitHub repository is gone; BlackArch packages from its own mirror of the source.

## Typical use cases

### Configure both ends

```
# /etc/multitun.conf (shared layout; server and client read their sections)
[all]
serv_addr = 198.51.100.10
serv_port = 80
ws_loc = mt
tun_nm = 255.255.255.0
tun_mtu = 1500

[server]
tun_dev = tun1
tun_addr = 10.0.0.1
p2paddr = 10.0.0.2
users = {'10.0.0.2': 'Password1'}

[client]
tun_dev = tun0
tun_addr = 10.0.0.2
password = Password1
```

Outcome: one file describes the service — `[all]` the WebSocket endpoint, `[server]` the server's TUN device plus the allowed users (TUN-IP to password), `[client]` this machine's TUN address and password. Restrict the file's permissions: it holds passwords.

### Start the server and connect a client

```
multitun -s        # on the server, as root (needs to create the TUN device)
multitun          # on the client, as root
```

Outcome: the server listens on `serv_port` speaking WebSocket at `ws_loc`; the client brings up its TUN device with the configured address, and `ssh user@10.0.0.1` reaches the server through the WebSocket. Browsers hitting the port without WebSocket get the `webdir` HTML instead. Keep a ping running in the background if connections drop — it doubles as keep-alive.

### Route all client traffic through the tunnel

Masquerade on the server, then swap the client's default route:

```
iptables -t nat -A POSTROUTING -s 10.0.0.0/24 -o eth0 -j MASQUERADE
echo 1 > /proc/sys/net/ipv4/ip_forward

ip route add 198.51.100.10 via <client-gateway> dev <client-interface> proto static
ip route del default
ip route add default via 10.0.0.1 dev tun0 proto static
```

Outcome: with the server NAT-ing the tunnel range out of `eth0` and the client defaulting through `tun0` (keeping only a host-route to the server via the old gateway), all client egress — not just SSH — crosses the firewall as WebSocket traffic.

*No corpus evidence — derived from upstream documentation.*
