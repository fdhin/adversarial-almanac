---
name: "vstt"
categories:
  - blackarch-tunnel
version: "0.5.3"
homepage: "http://www.wendzel.de/dr.org/files/Projects/vstt/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-11"
build-system: make
license: BSD
meta-checked: "2026-11-09"
gh-checked: "2026-11-09"
github-repo: "cdpxe/NetworkCovertChannels"
github-stars: 19
github-pushed: "2026-11-05"
github-archived: false
github-issues: 0
github-match: "exact"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 17
gh-activity-checked: "2026-11-09"
---

# vstt

> Very Strange Tunneling Tool — tunnels FIFO or TCP-stream data inside plain TCP, ICMP echo or POP3 look-alike traffic, with helper tools to bridge sockets and FIFOs.

## What it's for

VSTT (Steffen Wendzel, 2006/2016) moves data between two hosts while shaping the carrier to look like something a filtering device tolerates: a plain TCP connection (protocol `none`), ICMP echo packets (`icmp`, where ports are ignored), or POP3 client/server chatter (`pop3`, alpha quality). Each endpoint takes input from a local FIFO (`/tmp/.vstt_send2peer`) or a TCP socket and delivers received data to an output FIFO (`/tmp/.vstt_recvfpeer`) or socket; two shipped helpers bridge the gap — `reader` prints whatever arrives, and `s2f` (installed as `vstt-s2f`) connects a TCP port to the FIFOs in either direction, which is how real TCP applications such as SSH ride the tunnel. Needs root on both ends. It is deliberately minimal: no encryption, no authentication — protocol disguise only.

## Typical use cases

### Move a file over an ICMP channel

Sender (198.51.100.20):

```
sudo vstt -p icmp -r 9999 -t 10000 -a 198.51.100.10 -m 198.51.100.20
```

Receiver (198.51.100.10), in a second terminal:

```
sudo vstt-reader /tmp/.vstt_recvfpeer
```

Outcome: both vstt endpoints exchange ICMP echo packets carrying tunnel data (the port values are ignored for ICMP but must be present); on the sender, feed the channel with `sudo cat /etc/resolv.conf > /tmp/.vstt_send2peer` and the reader on the receiving host prints that file's contents as they arrive.

### Tunnel SSH over an allowed TCP port

On the SSH server host:

```
sudo vstt -p none -r 80 -t 80 -a 198.51.100.20 -c 22
```

On the SSH client host:

```
sudo vstt -p none -r 80 -t 80 -a 198.51.100.10 -c 10003 -s
```

Outcome: `-c` lets vstt manage the s2f bridge itself — on the server side it wires the tunnel FIFOs to the local SSH daemon on port 22, and `-c ... -s` on the client side opens a listening socket on 10003; `ssh -p 10003 user@127.0.0.1` now lands on the remote SSH server while the wire shows only TCP traffic on port 80.

### Watch both directions of the channel

```
sudo vstt-reader /tmp/.vstt_recvfpeer
```

Outcome: streams everything the local vstt endpoint receives from its peer to your terminal — useful as a quick liveness check for any tunnel configuration (any protocol) before wiring real applications onto the FIFOs.

*No corpus evidence — derived from upstream documentation.*

GitHub: the author (Steffen Wendzel, cdpxe) hosts vstt in his NetworkCovertChannels collection — the live source home of the tool.
