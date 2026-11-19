---
name: "ssl-hostname-resolver"
categories:
  - blackarch-recon
version: "1"
homepage: "http://packetstormsecurity.com/files/120634/Common-Name-Grabber-Script.html"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL3
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ssl-hostname-resolver

> Perl utility that walks a CIDR, IP range or single host, connects to each address's TLS port, and prints the certificate's Common Name next to the IP.

## What it's for

Naming infrastructure that DNS cannot: when an IP has no PTR record, the X.509 certificate it serves on HTTPS usually carries a hostname. The script expands the target specification, connects to every address (default port 443, 3-second timeout per host) and prints `IP => CN` lines — a quick way to map virtual hosts or identify what a range actually serves during reconnaissance. Written by Alejandro Hernandez (IOActive, 2013); installed by BlackArch as `ssl-hostname-resolver` (the upstream script is `ssl_hostname_resolver.pl`).

## Typical use cases

### Name every host in a subnet

```
ssl-hostname-resolver 198.51.100.0/24
```

Outcome: one `IP => CN` line per address; hosts that do not answer within the timeout print `(Couldn't connect to port 443)`.

### Scan a TLS service on a non-standard port

```
ssl-hostname-resolver 198.51.100.10 8443
```

Outcome: same output, connecting on port 8443 — the optional second argument overrides the default port for the whole run.

### Cover an explicit IP range

```
ssl-hostname-resolver 198.51.100.1-198.51.100.254
```

Outcome: expands the start-end range and resolves the CN of every host in it, for spans not expressible as a CIDR block.

*No corpus evidence — derived from upstream source (Packet Storm `ssl_hostname_resolver.pl`: usage banner, target forms and output format read from the script).*
