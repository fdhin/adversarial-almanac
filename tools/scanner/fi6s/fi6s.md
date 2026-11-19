---
name: "fi6s"
categories:
  - blackarch-scanner
version: "258.765fcf3"
homepage: "https://github.com/sfan5/fi6s"
packaged-by: [blackarch]
blackarch-builddate: "2026-08-02"
build-system: scripts
license: GAGPL
meta-checked: "2026-11-09"
github-repo: "sfan5/fi6s"
github-stars: 178
github-pushed: "2026-07-30"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 63
gh-activity-checked: "2026-11-09"
---

# fi6s

> Masscan-style IPv6 port scanner by sfan5: asynchronous raw-packet TCP/UDP/ICMPv6 scanning with banner grabbing, aimed at Internet-scale IPv6 discovery.

*No corpus evidence — derived from upstream documentation.*

## What it's for

fi6s does for IPv6 what masscan does for IPv4: it sends and processes raw packets asynchronously instead of letting the kernel's socket machinery limit the rate, auto-detecting source address, gateway and router MAC so you can start scanning immediately. Besides TCP port scanning it grabs banners by carrying just enough of its own TCP/IP stack to complete a connection and read the greeting, probes UDP services whose protocols it knows (`--list-protocols` shows which), and runs ICMPv6 ping sweeps. Know its limits, which are deliberate: it assumes a single gateway and a non-trivial routing table will be ignored, it does not do neighbor discovery (link-local targets are out of scope), and connection-tracking firewalls choke on its traffic volume. Linux is the supported platform.

## Typical use cases

### Port-scan an IPv6 range with a rate cap

```
sudo fi6s -p 80,8000-8100 --max-rate 170 2001:db8::/120
```

Outcome: scans the 256 addresses of the documentation subnet for TCP ports 80 and 8000-8100 at at most 170 packets per second, printing open ports to stdout in `list` format; prepend `--print-summary` or `--print-hosts` to preview how the target specification expands before any traffic is sent.

### Collect service banners

```
sudo fi6s -p 22 --banners 2001:db8::10
```

Outcome: for each open port fi6s completes enough of the handshake to capture what the service sends (an SSH greeting, HTTP header, etc.). On Linux the OS reserves an ephemeral source port automatically; elsewhere pick one with `--source-port` and firewall it (e.g. `ip6tables -A INPUT -p tcp --dport 12345 -j DROP`) so the kernel stack does not answer fi6s's connections.

### Probe UDP services

```
sudo fi6s -p 53 --banners --udp 2001:db8::1
```

Outcome: sends protocol-aware UDP probes and reports responders; useful output requires a port whose protocol fi6s knows how to interrogate — check `fi6s --list-protocols` first.

### Liveness-check addresses with ICMPv6

```
sudo fi6s --icmp 2001:db8::1
```

Outcome: an ICMPv6 ping scan of the target specification; hosts that answer are reported alive (round-trip time is not measured). Useful to shrink a huge range before port scanning, since target randomization (`--randomize-hosts 1`, the default) still traverses subnets sequentially.
