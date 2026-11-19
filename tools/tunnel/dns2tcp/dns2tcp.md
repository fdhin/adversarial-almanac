---
name: "dns2tcp"
categories:
  - blackarch-tunnel
version: "0.5.2"
homepage: "http://www.hsc.fr/ressources/outils/dns2tcp/index.html.en"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2014-06-13"
build-system: autotools
license: GPL-1.0-only
meta-checked: "2026-11-09"
github-repo: "alex-sector/dns2tcp"
github-stars: 234
github-pushed: "2024-06-08"
github-archived: false
github-issues: 3
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dns2tcp

> Relays TCP connections over DNS queries — dns2tcpc client in the restricted network, dns2tcpd server on an authoritative name server (Olivier Dembour, HSC).

## What it's for

Getting TCP traffic out of a network where only DNS is allowed to leave. The server (`dns2tcpd`) runs on a host that is authoritative for a DNS zone and publishes named resources (`name:host:port`); the client (`dns2tcpc`) encodes TCP streams into DNS requests for that zone, so traffic rides the normal resolver chain to your server and back. There is no encryption — the pre-shared key is a simple identification mechanism, not confidentiality — so tunnel an already-encrypted protocol (SSH, an SSL tunnel) through it. Each connection is carried over multiple TXT or KEY records; the server side is not supported on Windows.

## Typical use cases

### Run the server side on the authoritative DNS host

`~/.dns2tcpdrc`:

```
listen = 198.51.100.10
port = 53
user = nobody
key = S3cret
domain = tunnel.example.com
resources = ssh:127.0.0.1:22, smtp:127.0.0.1:25
```

```
dns2tcpd -F -d 1
```

Outcome: dns2tcpd answers DNS traffic on port 53 for `tunnel.example.com`, exposing the listed resources to clients that present the key; `-F` keeps it in the foreground and `-d 1` prints one level of debug. The zone's NS record must point at this host for resolver traffic to reach it.

### Discover what resources a server offers

From inside the restricted network:

```
dns2tcpc -z tunnel.example.com -k S3cret 198.51.100.10
```

Outcome: prints the available connection list (here `ssh` and `smtp`). The trailing address is the DNS resolver to use — the network's own resolver works and is the whole point; if omitted, the first entry in `/etc/resolv.conf` is used.

### Relay one resource to a local port

```
dns2tcpc -z tunnel.example.com -k S3cret -r ssh -l 2222 198.51.100.10
```

Outcome: the client listens on local port 2222; connections are chopped into DNS requests, reassembled at the dns2tcpd host, and forwarded to the `ssh` resource (`127.0.0.1:22` there). An `ssh -p 2222 user@127.0.0.1` from the client machine now lands on the server's SSH daemon, fully carried over DNS.

### Drive the client from its config file

`~/.dns2tcprc`:

```
domain = tunnel.example.com
resource = ssh
local_port = 2222
key = S3cret
server = 198.51.100.10
```

```
dns2tcpc
```

Outcome: a bare `dns2tcpc` reads `~/.dns2tcprc` (or a file passed with `-f`) and starts the same relay without any flags — convenient for a long-lived tunnel you re-establish repeatedly. Other useful client switches: `-e <program>` runs a program whose I/O is redirected through the tunnel instead of opening a listener, and `-T KEY|TXT` selects the DNS record type.

*No corpus evidence — derived from upstream documentation.*
