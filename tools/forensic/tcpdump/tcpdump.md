---
name: "tcpdump"
categories:
  - forensics
version: "4.99.7-1"
homepage: "https://www.tcpdump.org/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-09-22"
build-system: cmake
meta-checked: "2026-11-09"
github-repo: "the-tcpdump-group/tcpdump"
github-stars: 3262
github-pushed: "2026-10-21"
github-archived: false
github-issues: 136
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 113
gh-activity-checked: "2026-11-09"
---

# tcpdump

> Command-line packet capture and filtering with the standard BPF expression syntax.

## What it's for

tcpdump captures live packets from a network interface or reads saved captures, filtering with the libpcap BPF expression language (host, net, port, protocol, TCP flags). It prints one-line summaries or dumps raw payload bytes, and reads and writes the pcap format every other analyzer understands, which makes it the default capture tool over SSH or on console-only hosts. It shows packets, not reassembled sessions — for per-connection stream reassembly use tcpflow or tcpick instead.

## Typical use cases

### Capture live traffic with numeric output

Watch an interface in real time without DNS and port-name lookups.

```
tcpdump -nn -i eth0
```

Outcome: prints a one-line summary per packet seen on eth0; `-nn` shows IP addresses and ports numerically (no reverse DNS, no service-name mapping). Add `--immediate-mode` when piping the output into another command so packets are processed as they arrive instead of in buffered batches.

### Capture on every interface at once

Catch traffic regardless of which interface it arrives on.

```
tcpdump -i any -nn port 80
```

Outcome: captures on all interfaces simultaneously (Linux cooked capture, link type `linux_sll2`) and shows every port-80 packet on the host; on machines with bridges, VPNs or docker interfaces this avoids guessing the right interface name.

### Save a capture to a pcap file

Record traffic for later analysis elsewhere.

```
tcpdump -i eth0 -w capture.pcap port 389
```

Outcome: writes matching raw packets to capture.pcap (nothing is decoded to stdout while a savefile is active); read it back later with `tcpdump -r` or a GUI analyzer. Add `-G 60 -W 5` before the `-w` file to rotate it every 60 seconds keeping five files.

### Watch ICMP as an out-of-band confirmation channel

Prove that blind command execution or exfiltration works when the application never returns output.

```
tcpdump -ni eth0 icmp
tcpdump -ni eth0 icmp and host 198.51.100.10
```

Outcome: prints every ICMP packet seen, echo-request payloads inline; run it (on a VPN that is typically the tun0 interface) while triggering a ping-based payload — arriving packets confirm the command executed. Narrow with `and host` when several hosts share the segment.

### Filter by address, subnet, VLAN, or TCP flags

Select exactly the conversations of interest.

```
tcpdump -nn -i eth0 host 198.51.100.10
tcpdump -nn -i eth0 src net 198.51.100.0/24
tcpdump -nn -i eth0 vlan
tcpdump -nn -i eth0 'tcp[tcpflags] & (tcp-syn|tcp-fin) != 0'
```

Outcome: each variant keeps only matching packets — `host` (with `src`/`dst` for direction) by address, `net` by subnet, `vlan` for 802.1Q-tagged frames, and the `tcp[tcpflags]` expression for connection openings and closings only, a compact log of who talked to whom.

### Display packet payloads in ASCII or hex

Read cleartext protocols inline.

```
tcpdump -nn -i eth0 -A port 80
tcpdump -nn -i eth0 -X port 80
```

Outcome: `-A` prints each payload minus its link-level header as ASCII, `-X` prints a hex-and-ASCII dump of each packet; use `-s 0` to capture the full packet when a smaller snap length is configured.

### Harvest cleartext credentials

Grab logins from unencrypted protocols and stop automatically.

```
tcpdump -nn -i eth0 -A -c 20 port 21 | grep -iE 'user|pass'
```

Outcome: renders the first 20 FTP packets as ASCII and filters the stream for USER/PASS lines; `-c 20` ends the capture on its own, so the command is a one-shot sampler instead of an endless listener.

### Observe SMB sessions for account names

See who is authenticating on a Windows segment.

```
tcpdump -nn -i eth0 port 445 or port 139
```

Outcome: prints SMB session traffic; session-setup packets decode with the client's account name visible in the summary line, exposing active users and their hosts without touching the servers.

### Read a saved capture and rewrite it filtered

Carve one host or service out of a large capture.

```
tcpdump -r capture.pcap -nn host 198.51.100.10
tcpdump -r capture.pcap -w http.pcap port 80
```

Outcome: `-r` reads the pcap offline with the same BPF filters applying and no interface or privileges needed; combined with `-w`, only matching packets are copied into the new file for hand-off or further analysis.

### Run a command at every capture rotation

Hook script execution into tcpdump's savefile rotation.

```
sudo tcpdump -ln -i eth0 -w /dev/null -W 1 -G 1 -z /path/to/rotate.sh
```

Outcome: rotates the (discarded) savefile every second and runs `rotate.sh <savefile>` after each rotation with tcpdump's own privileges — the documented `-z gzip` pattern for compressing captures on rotation. Because the postrotate command inherits root when tcpdump runs under sudo, any sudo grant of tcpdump is effectively a root shell.
