---
name: "argus"
categories:
  - blackarch-networking
version: "3.0.8.2"
homepage: "http://qosient.com/argus/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL3
meta-checked: "2026-11-09"
github-repo: "openargus/argus"
github-stars: 79
github-pushed: "2026-10-29"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 7.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 115
gh-activity-checked: "2026-11-09"
---

# argus

> The Argus flow-audit sensor — turns live traffic or packet captures into a persistent network transaction audit log.

## What it's for

Argus (QoSient, LLC) is the data-generation half of the Audit Record Generation and Utilization System: a daemon that reads packets from a network interface or from tcpdump/snoop-style capture files and writes Argus flow records — per-transaction status covering all network activity, not just IP — to a file, stdout, a remote stream, or a TCP socket for clients. The resulting audit trail supports forensics, trending and near-real-time alerting; you process it with the client suite in [[argus-clients]] (`ra`, `racluster`, `ratop`, ...). Remote client access can be protected with SASL and tcp_wrappers. Argus is not a full-packet capture store and does no analysis itself — pair it with the clients.

## Typical use cases

### Record flows from a live interface

```
sudo argus -i eth0 -w argus.out
```

Outcome: appends argus(5) flow records for every observed transaction to `argus.out`; analyze later with `ra -r argus.out`.

### Run as a daemon serving remote clients

```
sudo argus -d -i eth0 -P 561
```

Outcome: argus detaches and accepts client connections on TCP/561 so hosts on the network can attach live with `ra -S host:561`; add `-e '"id1"'` to tag records with a source identifier when running several sensors.

### Convert an existing capture to flow records

```
argus -r capture.pcap -w capture.argus
```

Outcome: reads the tcpdump/snoop capture file and writes the equivalent flow audit (netflow files are also readable with a `cisco:` type prefix); when `-r` is given, argus does not open a remote-access listener.

### Stream records to a remote collector

```
sudo argus -i eth0 -w argus-udp://collector.example.com:561
```

Outcome: instead of a local file, each flow record is sent over UDP to the collector host (default port 561) for centralized aggregation; persistent settings belong in a config file passed with `-F /etc/argus.conf`.

*No corpus evidence — derived from upstream documentation.*
