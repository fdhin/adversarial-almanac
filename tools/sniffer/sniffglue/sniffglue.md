---
name: "sniffglue"
categories:
  - sniffer
homepage: "https://github.com/kpcyrd/sniffglue"
origin: "awesome-pentest"
recipe: "upstream"
packaged-by: [arch, nixpkgs]
build-system: rust
github-repo: "kpcyrd/sniffglue"
github-stars: 1270
github-match: "exact"
gh-checked: "2026-11-14"
github-commits-year: 12
gh-activity-checked: "2026-11-14"
worth: 6.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-14"
license: GPLv3
---

# sniffglue

> Secure, multithreaded network sniffer in Rust that prints useful, human-readable summaries of live traffic by default.

## What it's for

sniffglue (kpcyrd) is a sniffer built to run safely on untrusted networks: packets are parsed concurrently by a thread pool across all CPU cores, the parser must not crash on hostile input, and after setup the process seals itself with a two-stage seccomp filter, chroot and privilege drop (configured via `/etc/sniffglue.conf`). Out of the box it shows only the interesting layers — DHCP, DNS, TLS and HTTP — with verbosity flags widening the filter (ARP at `-v`; cjdns, SSDP, Dropbox beacons and valid-UTF-8 packets at `-vv`; near-everything at `-vvv`/`-vvvv`). Parsed link/network/transport protocols include ethernet, PPP, IPv4/IPv6, ARP, TCP, UDP, ICMP; 802.11 is not parsed yet. It is a passive observer — no injection, MITM or file capture; for writing pcaps use a classic capture tool next to it.

## Typical use cases

### Live overview of a noisy interface

```
sudo sniffglue enp0s25
```

Outcome: one readable summary line per interesting packet (DHCP exchanges, DNS lookups with queried names, TLS connection details, HTTP requests) — the high-signal view of what an interface is doing, with junk filtered out by default.

### Widen the filter for deeper inspection

```
sudo sniffglue -vv enp0s25
```

Outcome: `-v` additionally decodes ARP traffic; `-vv` adds cjdns eth beacons, SSDP, Dropbox beacons and any packet whose payload is valid UTF-8 — step up one `-v` at a time until the layer you're hunting for appears (`-vvv` almost everything, `-vvvv` everything).

### Sniff inside a container setup

```
docker run -it --init --rm --net=host sniffglue eth0
```

Outcome: an ~11 MB image (built with `docker build -t sniffglue .`) runs the sniffer against the host network stack — handy for debugging container networking without installing anything on the host.

*No corpus evidence — derived from upstream documentation.*
