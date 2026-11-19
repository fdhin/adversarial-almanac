---
name: "nsntrace"
categories:
  - blackarch-sniffer
version: "86.1d5adce"
homepage: "https://github.com/jonasdn/nsntrace"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-03"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "jonasdn/nsntrace"
github-stars: 548
github-pushed: "2026-09-29"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# nsntrace

> Capture a pcap of one process only, by running it inside its own Linux network namespace.

## What it's for

When you need to know exactly which hosts and ports a single program contacts — an installer, an updater, an untrusted binary — capturing the whole interface buries the answer in system noise. nsntrace launches the command you give it inside a private Linux network namespace, forwards that namespace's traffic to the real network through a virtual Ethernet pair with NAT, and records only the namespace with libpcap. The output is a pcap containing just the traced process's packets, ready for Wireshark or tshark. Constraints: it must run as root (raw sockets and iptables), the traced process's source address is the NAT address of the virtual device rather than the host's own, and DNS through a local stub resolver on a loopback address (systemd-resolved, dnsmasq) does not work inside the namespace — `--use-public-dns` swaps in public resolvers for the trace.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Record one command's traffic to a pcap

Get a capture that contains only the packets of a single process.

```
sudo nsntrace -d eth0 -o app.pcap wget https://example.com/
```

Outcome: nsntrace prints "Starting network trace of 'wget' on interface eth0" plus the NAT address the trace uses, the command runs normally, and on exit the packet count is printed and `app.pcap` holds only that process's traffic — inspect it with `tshark -r app.pcap` or Wireshark.

### Watch the trace live instead of writing a file

Stream the pcap straight into tshark.

```
sudo nsntrace -f tcp -o - curl -s https://example.com 2> /dev/null | tshark -r -
```

Outcome: `-o -` sends the pcap to stdout, so packets are decoded by tshark in real time and no capture file is written; the redirect keeps nsntrace's status messages off the pipe.

### Restrict the capture with a BPF filter

Record only the protocol or host of interest.

```
sudo nsntrace -d eth0 -f 'port 443' some-client
```

Outcome: only packets matching the libpcap capture filter are recorded to the default `nsntrace.pcap` — the same filter semantics as tcpdump, so e.g. `-f 'host 198.51.100.10'` traces just one peer.

### Trace on a host with a local DNS stub resolver

Fix name resolution failing inside the namespace.

```
sudo nsntrace --use-public-dns -d eth0 firefox
```

Outcome: resolv.conf inside the namespace is overridden with Quad9, Cloudflare, Google and OpenDNS resolvers, so DNS queries succeed and appear in the trace as traffic to those public resolvers instead of dead loopback stub lookups.
