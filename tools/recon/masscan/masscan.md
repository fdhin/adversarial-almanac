---
name: "masscan"
categories:
  - information-gathering
version: "1.3.2-2"
homepage: "https://github.com/robertdavidgraham/masscan"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2024-07-12"
build-system: make
license: AGPL3
meta-checked: "2026-11-09"
github-repo: "robertdavidgraham/masscan"
github-stars: 26126
github-pushed: "2026-10-05"
github-archived: false
github-issues: 414
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 21
gh-activity-checked: "2026-11-09"
---

# masscan

> Asynchronous TCP/UDP port scanner built to sweep huge address ranges from one machine.

## What it's for

Finding open ports across large IPv4/IPv6 ranges — a /24, a /8, or the whole Internet — using its own userspace TCP/IP stack and a transmit/receive design that sends packets as fast as you allow (default is only 100 packets/second; raise it deliberately). It complements rather than replaces nmap: targets must be IP addresses or ranges (no DNS names), there are no default ports (`-p` is mandatory), every scan is SYN-only with no ping and randomized host order, and the usual workflow is masscan to find open ports, then nmap for service/version detection. Banner grabbing is the deepest interaction it does. It transmits raw packets, so it normally runs as root.

Commands below are verified against the upstream README and the packaged source.

## Typical use cases

### Full TCP port range on one host

Find every open TCP port on a single target at a controlled rate.

```
masscan -p0-65535 198.51.100.10 --max-rate 1000 -e tun0
```

Outcome: SYN-scans all 65,536 TCP ports at up to 1000 packets/second out of the `tun0` interface and prints one line per responding port (`open tcp <port> <ip> <timestamp>`). `--rate` is an alias for `--max-rate`; redirect stdout to a file to keep the port list for follow-up nmap runs.

### Combined TCP and UDP full-range sweep

Probe both protocols in a single pass over one host.

```
masscan -p1-65535,U:1-65535 198.51.100.10 --rate 1000 -e tun0 > ports.txt
```

Outcome: the `U:` prefix marks the UDP port range in the same `-p` argument; UDP probes carry the scanner's bundled payloads for known services. Open UDP ports are reported as `open udp` lines, and `ports.txt` holds the combined result list.

### Top-ports quick check

Test only the common service ports.

```
masscan -p21,22,80,443 198.51.100.10 --rate 1000
```

Outcome: scans just the listed ports (comma-separated values and ranges like `8000-8100` mix freely) and finishes in seconds; useful as a first pass before committing to a full 65k sweep.

### Subnet sweep

Discover hosts running a service across an address range.

```
masscan -p80,443 198.51.100.0/24 --rate 10000
```

Outcome: probes every address in the /24 for the web ports in randomized order and prints each open port with its IP; multiple ranges (IPv4 and IPv6 together) can be appended as further arguments.

### Grab service banners

Complete the connection and capture what the service announces.

```
masscan 198.51.100.0/24 -p80 --banners --source-port 61000
```

Outcome: after the SYN/ACK, masscan's own TCP stack finishes the handshake and prints banners for protocols it understands (HTTP, FTP, SSH, SMTP, POP3, IMAP4, SSL, telnet, VNC, RDP, SMBv1/v2, memcached). Because the kernel stack would RST the SYN-ACK, run it with a firewall rule such as `iptables -A INPUT -p tcp --dport 61000 -j DROP`, or give it a dedicated local IP with `--source-ip`.

### Write results in a structured format

Keep the findings in a parseable file instead of raw stdout.

```
masscan -p22,80 198.51.100.0/24 -oL open-ports.txt
```

Outcome: writes the list format — one `open tcp <port> <ip> <timestamp>` line per result. `-oG` produces nmap-style grepable output, `-oJ` JSON, and `-oX` XML; the long forms are `--output-format <fmt>` plus `--output-filename <file>`.

### Exclude ranges from a wide scan

Keep known-sensitive or off-limits blocks out of the sweep.

```
masscan 198.51.100.0/24 -p80,443 --excludefile exclude.txt
```

Outcome: every address or CIDR listed one-per-line in `exclude.txt` is skipped. The same setting can live in `/etc/masscan/masscan.conf` (or a custom config) so exclusions apply automatically to every scan.

### Report open ports only, nmap-style

Filter output down to hits when scripting around the scanner.

```
masscan -p80,443 198.51.100.0/24 --open-only
```

Outcome: only ports found open are emitted. masscan already behaves as if `-sS -Pn -n --randomize-hosts --send-eth` were set; `masscan --nmap` prints the full list of accepted nmap-compatible options.

### Save and replay a scan configuration

Make a large scan repeatable and adjustable.

```
masscan -p0-65535 198.51.100.0/24 --rate 10000 --echo > full-sweep.conf && masscan -c full-sweep.conf
```

Outcome: the first invocation sends no packets and writes the complete parsed configuration to `full-sweep.conf`; the second replays it (any flag added on the replay command line overrides the file, e.g. `masscan -c full-sweep.conf --rate 1000`).
