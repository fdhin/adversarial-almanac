---
name: "samplicator"
categories:
  - blackarch-networking
version: "175.ceeb1d2"
homepage: "https://github.com/sleinen/samplicator"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2021-10-17"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "sleinen/samplicator"
github-stars: 426
github-pushed: "2023-10-01"
github-archived: false
github-issues: 44
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# samplicator

> Replicates incoming UDP datagrams — typically flow exports — to any number of collectors.

## What it's for

The `samplicate` utility (samplicator) receives UDP datagrams on one port and resends a copy to each destination you list, optionally keeping only one in N datagrams per receiver and spoofing the original source address. Its bread and butter is NetFlow/IPFIX and other monitoring feeds: point exporters at it once and you can feed several collectors (or trial a new tool) without reconfiguring every exporter. It only replicates UDP — it does not parse, translate or aggregate the datagrams.

## Typical use cases

### Fan out a flow feed to several collectors

Send the same export stream to two analysis systems.

```
samplicate -p 2055 198.51.100.10/2055 198.51.100.20/2056
```

Outcome: samplicator listens on UDP port 2055 and resends every received datagram to 198.51.100.10:2055 and 198.51.100.20:2056; destinations use the syntax `addr[/port[/interval[,ttl]]]` with port defaulting to 2000, and `-s` binds a specific receive address.

### Send only a sample to a secondary collector

Keep the full feed on the primary box but give a test system a fraction of it.

```
samplicate -p 2055 198.51.100.10/2055 198.51.100.20/2055/50
```

Outcome: the first receiver gets every datagram, the second gets one copy per 50 received — enough traffic for tool evaluation without the full load.

### Preserve the exporter's source address

Make collectors see the original sender instead of the replicator.

```
samplicate -S -p 2055 198.51.100.10/2055
```

Outcome: forwarded datagrams carry the exporter's own IP as source address (`-S` spoofs it); add `-n` to skip UDP checksum computation, and make sure return traffic routes to the real sender, not to the replicator.

### Route by sender with a config file

Replicate specific exporters' traffic to dedicated receivers.

```
samplicate -c samplicate.conf
```

Outcome: with config-file lines like `198.51.100.10/255.255.255.255: 198.51.100.20/2055`, receivers named on the command line keep receiving everything while config-listed receivers receive only datagrams whose source address matches the given address/mask pair.

*No corpus evidence — derived from upstream documentation.*
