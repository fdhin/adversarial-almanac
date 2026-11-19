---
name: "hyenae"
categories:
  - blackarch-networking
version: "0.36_1"
homepage: "http://sourceforge.net/projects/hyenae/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2019-11-16"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "r-richter/hyenae"
github-stars: 26
github-pushed: "2020-06-24"
github-archived: true
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hyenae

> Highly flexible network packet generator for reproducing low-level ethernet attacks (ARP poisoning, ICMP/TCP/UDP/DNS/DHCP floods, HSRP hijack) with wildcard address randomization and a clusterable remote daemon.

## What it's for

Hyenae crafts and sends raw packets to reproduce MITM, DoS and DDoS scenarios — ARP-cache poisoning, ICMP-echo and Smurf floods, TCP-SYN floods and blind connection resets, UDP and DNS-Query floods, DHCP starvation and Cisco HSRP hijacking — to test firewall rules, flood detection and intrusion detection systems. Its distinguishing feature is address patterns: `%` wildcards inside hardware/IP/port strips randomize any octet while still producing valid addresses, with the protocol and IP version derived automatically from the pattern. It does not exploit services or capture traffic; it only generates packets. A bundled daemon, `hyenaed`, lets a `hyenae` client execute attacks on remote hosts for distributed (clustered) generation. Written by Robin Richter (2009-2010); upstream is dormant.

## Typical use cases

### Enumerate interfaces and available attacks

Discover the interface indexes and attack names needed for direct invocation.

```
hyenae -l
hyenae -L
```

Outcome: `-l` prints every network interface with its index (usable via `-I`), `-L` prints every supported attack name (e.g. `arp-reply`, `icmp-echo`, `tcp-syn`) for use with `-a`.

### Run the interactive attack assistant

Build an attack interactively instead of memorizing per-attack flags.

```
hyenae
```

Outcome: starts the assisted attack wizard, which walks through the chosen scenario (e.g. ARP-Cache poisoning, DHCP starvation), asks only for the parameters that attack needs, and then executes it.

### Send crafted ARP replies (ARP-cache poisoning)

Poison a target's ARP cache by claiming another host's IPv4 address.

```
hyenae -a arp-reply -i eth0 \
  -s 00:11:22:33:44:55 -d 00:aa:bb:cc:dd:ee \
  -S 00:11:22:33:44:55-198.51.100.10 \
  -D 00:aa:bb:cc:dd:ee-198.51.100.20 \
  -c 100 -e 10
```

Outcome: sends 100 ARP replies (at least 10 ms apart) to `198.51.100.20` asserting that `198.51.100.10` lives at `00:11:22:33:44:55`, poisoning the target's cache for MITM testing.

### Flood with randomized sources

Stress flood detection with ICMP echo requests from randomized addresses.

```
hyenae -a icmp-echo -i eth0 \
  -s 00:11:22:33:44:55-198.51.100.% \
  -d 00:aa:bb:cc:dd:ee-198.51.100.20 \
  -c 1000 -e 1
```

Outcome: sends 1000 ICMP echo requests to `198.51.100.20` with randomly chosen last-octet source addresses, exercising rate limiting and IDS signatures; replace a whole strip with `%` to randomize it completely.

### Distribute generation across remote daemons

Run one `hyenaed` on a second host and drive it from the local `hyenae` client.

```
hyenaed -i eth0 -c 100000 -a 198.51.100.50 -p 20175 -k s3cr3t
hyenae -a icmp-echo -i eth0 -s %-% -d %-198.51.100.20 \
  -r 198.51.100.50@20175+s3cr3t
```

Outcome: the daemon listens on `198.51.100.50` port 20175 (password-protected) with a 100000-packet limit; the client connects, authenticates and executes the attack on the daemon's interface so traffic originates from the remote host. Passing `-R daemon-list.txt` instead of `-r` clusters the same attack across every daemon in the file.

*No corpus evidence — derived from upstream documentation.*
