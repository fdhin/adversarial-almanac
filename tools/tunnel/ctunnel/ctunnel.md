---
name: "ctunnel"
categories:
  - blackarch-tunnel
version: "0.7"
homepage: "http://alienrobotarmy.com/ctunnel"
packaged-by: [blackarch]
blackarch-builddate: "2015-01-17"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "alienrobotarmy/ctunnel"
github-stars: 61
github-pushed: "2022-10-21"
github-archived: false
github-issues: 6
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ctunnel

> Tunnel and/or proxy TCP or UDP connections through a pre-shared-key encrypted channel, or build a point-to-point VPN between two hosts (Jess Mahan).

## What it's for

Forwarding and optionally encrypting any TCP- or UDP-based protocol (HTTP, Telnet, MySQL, VNC, DNS, ...) between two hosts, with no certificates or CA — both ends share a Key and IV that ctunnel prompts for on first run and stores in `~/.passkey`. You must use a stream cipher (or a block cipher in a stream mode: cfb, ofb, ctr), passed with `-C`; in libgcrypt builds (what most distro packages compile) the mode is a separate `-M` flag. It can also run as a plaintext proxy, chain connections through intermediary hosts, compress traffic (`-z`), and — in VPN mode — bridge two networks over a TUN/TAP device or PPP. It is deliberately low-ceremony point-to-point crypto, not TLS: protect `~/.passkey`, since anyone holding the Key and IV can decrypt the stream.

## Typical use cases

### Encrypt a plaintext service end to end

Server side (198.51.100.20, service on its loopback):

```
ctunnel -s -l 198.51.100.20:2222 -f 127.0.0.1:3306 -C aes256 -M cfb
```

Client side:

```
ctunnel -c -l 127.0.0.1:3306 -f 198.51.100.20:2222 -C aes256 -M cfb
```

Outcome: the client's `127.0.0.1:3306` accepts connections, carries them encrypted to the server's port 2222, and exits toward the server's local MySQL — `mysql -h 127.0.0.1` now rides the tunnel. Both ends prompt for a Key and IV on first run and remember them in `~/.passkey`. In OpenSSL builds the cipher and mode are one flag: `-C aes-256-cfb`.

### Forward a port without encryption

```
ctunnel -n -c -l 127.0.0.1:2202 -f 198.51.100.20:22 -C plain
```

Outcome: a plain TCP redirector — everything hitting the client's `127.0.0.1:2202` is forwarded verbatim to port 22 on 198.51.100.20. Use it where encryption is handled elsewhere or unwanted; `-n` keeps the process in the foreground.

### Carry UDP traffic (for example DNS)

Server side:

```
ctunnel -U -s -l 0.0.0.0:5001 -f 127.0.0.1:53 -C aes256 -M cfb
```

Client side:

```
ctunnel -U -c -l 0.0.0.0:53 -f 198.51.100.20:5001 -C aes256 -M cfb
```

Outcome: `-U` switches both ends to UDP; the client's port 53 now forwards encrypted datagrams to the server, which relays them to its local resolver — `dig @127.0.0.1 example.com` resolves through the tunnel. Without `-U`, ctunnel tunnels TCP by default.

### Bridge two networks in VPN mode

Server side:

```
ctunnel -V -U -n -s -l 198.51.100.20:1024 -C aes128 -M cfb -r 198.51.100.0/24
```

Client side:

```
ctunnel -V -U -n -c -f 198.51.100.20:1024 -C aes128 -M cfb -r 203.0.113.0/24
```

Outcome: `-V` builds a point-to-point VPN over a TUN/TAP device on both hosts, and each `-r` pushes the other side's subnet as a route. Hosts behind each endpoint can then reach the other network (enable IP forwarding and any firewall rules yourself); `-P '/usr/sbin/pppd ...'` swaps TUN/TAP for PPP if no tun device is available.

*No corpus evidence — derived from upstream documentation.*
