---
name: "mptcp"
categories:
  - blackarch-networking
version: "1.9.0"
homepage: "http://packetstormsecurity.com/files/119132/Mptcp-Packet-Manipulator.9.0.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "FelipeEcker/nsoq"
github-stars: 3
github-pushed: "2016-02-29"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-09"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mptcp

> MpTcp — a low-level TCP/IP packet manipulator for probing, spoofing and stress-testing, unrelated to the Multipath TCP protocol despite the name.

## What it's for

MpTcp (by Felipe Ecker "Khun", hexcodes.org, 2003–2013) is an hping-style packet tool: it crafts and sends raw ICMP, TCP, UDP and ARP/RARP frames with control over MAC and IP addresses, ports, TCP flags, packet size, TTL and thread count, and it can spoof the source address of every field. Despite the name it has nothing to do with Multipath TCP — it predates the tool family of that name and is a general packet manipulator. Beyond probing it includes listen modes (`-lI`/`-lT`/`-lU`/`-lA`), ARP-ping of ICMP-filtered hosts, aggressive options that are outright attacks (MAC flood `-f`, ARP cannon `-r`, HTTP stress modes), and an "RSOI" mode that hands control of the running instance to an IRC channel. It requires root (the binary refuses to run otherwise); the upstream site is gone and the packaged source is the PacketStorm tarball.

GitHub: the author's FelipeEcker/mptcp repository was renamed to FelipeEcker/nsoq (GitHub 301 redirect on the old path); nsoq is the live successor of the MpTcp packet manipulator.

## Typical use cases

### Probe a host with ICMP echo requests

```
sudo mptcp -d target.example.com -Ie
```

Outcome: sends ICMP echo-request packets to the target and prints the echo replies if it is alive; `-c` repeats every second, `-x 512` sets a 512-byte packet size, `-t 50` the TTL, and `-z` skips waiting for replies.

### Test a TCP port with a SYN packet

```
sudo mptcp -d target.example.com -p 80 -Ts
```

Outcome: sends a single TCP packet with the SYN flag set to port 80 and displays any response; `-Tc`/`-Ta`/`-Tr`/`-Tf`/`-Tx`/`-Tn` switch to full connect(), ACK, RST, FIN, XMAS and NULL probes, and `-D` prints replies tcpdump-style.

### Discover hosts that filter ICMP

```
sudo mptcp -a 198.51.100.10
```

Outcome: ARP-ping mode — sends ARP REQUEST packets to the given IP and waits for the ARP reply, detecting live hosts that silently drop ICMP echo requests.

### Send a spoofed-source UDP burst

```
sudo mptcp -s ATTACKER_IP -d target.example.com -u -p 9000 -q 20
```

Outcome: sends exactly 20 empty UDP packets from forged source address `ATTACKER_IP` to target port 9000 (`-F <usec>` adds a delay between packets, `-c` sends continuously instead).

### Listen for arriving packets

```
sudo mptcp -lI
```

Outcome: enters ICMP listen mode and prints every unicast ICMP packet observed on the wire; `-lT -P <port>`/`-lC -P <port>`/`-lU -P <port>`/`-lA` do the same for TCP connections, TCP accepts, UDP and ARP/RARP traffic.

*No corpus evidence — derived from upstream documentation.*
