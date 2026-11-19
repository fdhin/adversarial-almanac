---
name: "admid-pack"
categories:
  - blackarch-spoof
version: "0.1"
homepage: "http://packetstormsecurity.com/files/10080/ADMid-pkg.tgz.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: make
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# admid-pack

> The ADM crew's 1998 DNS spoofing toolkit: five C utilities that forge, hijack and crash DNS traffic by sniffing queries or predicting and brute-forcing DNS transaction IDs.

*No corpus evidence — derived from upstream documentation.*

## What it's for

A historical pack from the French ADM crew (shipped as `ADMid-pkg.tgz`, January 1998) covering the whole DNS attack repertoire of its era: passive hijacking of lookups on a LAN (`ADMsniffID`), ID-prediction spoofing when you control an authoritative name server (`ADMsnOOfID`), blind brute-forcing of DNS query IDs from any rooted box (`ADMnOg00d`), a brute ID spoofer (`ADMkillDNS`), and a tool to flood a name server until it stops answering (`ADMdnsfuckr`). The bundled `Docs/HOWTO` documents each tool with worked examples. Modern resolvers randomize and verify query IDs (0x0 hashing, source-port randomization), so the ID-guessing attacks are largely defunct today — the pack's value is understanding how cache poisoning worked before those hardenings. All tools need root and most need libpcap.

## Typical use cases

### Hijack lookups on a LAN by sniffing

One sentence of intent: answer DNS queries faster than the real resolver by listening on the wire.

```
ADMsniffID eth0 198.51.100.10 fake.host.example 12
```

Outcome: forges replies on `eth0` claiming that reverse lookups (type 12 = PTR; use 1 for A records) resolve to the name `fake.host.example` at `198.51.100.10`. Requires root and works only on the local network segment; `nslookup <ip>` on a victim machine then returns the injected name.

### Spoof a record via DNS ID prediction

One sentence of intent: race a victim name server's legitimate reply using a predictable transaction ID.

```
ADMsnOOfID ppp0 ns.target.example yourdomain.example ns.yourdomain.example 12 fake.host.example 198.51.100.10 ns.auth.example
```

Outcome: predicts the query ID the victim resolver (`ns.target.example`) will use, then sends a forged answer mapping `198.51.100.10` to `fake.host.example`. Requires root on a name server with authority over `yourdomain.example` (or the ability to sniff its traffic); the last argument names a server with authority over the spoofed name so the answer looks legitimate.

### Brute-force a resolver's DNS ID blindly

One sentence of intent: guess transaction IDs without any foothold on a DNS server.

```
ADMnOg00d 198.51.100.5 ns.helper.example helper.example ns.victim.example 12 fake.host.example 198.51.100.10 ns.auth.example 7000
```

Outcome: hammers `ns.victim.example` with candidate replies whose IDs start at 7000 (supply the current ID if known), using `ns.helper.example` only to learn the ID counter; once an ID hits, the resolver caches the forged mapping for `fake.host.example`.

### Knock out a name server

One sentence of intent: degrade a resolver with a flood of traffic.

```
ADMdnsfuckr ns.target.example
```

Outcome: floods the named host with DNS traffic until it stops responding — a denial-of-service against the resolver, not a spoofing attack.
