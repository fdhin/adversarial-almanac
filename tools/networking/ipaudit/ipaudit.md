---
name: "ipaudit"
categories:
  - blackarch-networking
version: "1.1"
homepage: "http://ipaudit.sourceforge.net"
packaged-by: [blackarch]
blackarch-builddate: "2019-08-24"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "aur-archive/ipaudit"
github-stars: 0
github-pushed: "2015-08-16"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ipaudit

> IP traffic summarizer: tallies bytes and packets per connection (host pair, protocol, ports) from a live interface or a saved pcap, with BPF filtering, timestamps, MAC addresses and SQL-ready output.

## What it's for

IPAudit listens on a network interface in promiscuous mode (or reads a previously saved pcap dump) and accumulates per-connection totals: bytes and packet counts in each direction for every IP-address pair, protocol and (for TCP/UDP) port pair. Use it for lightweight bandwidth accounting and who-talked-to-whom summaries without keeping per-packet records; it is not an IDS or a full capture tool, though it can optionally dump packets to a pcap file while tallying. Output columns are: host1 IP, host2 IP, protocol, host1 port, host2 port, host1 bytes, host2 bytes, host1 packets, host2 packets (IP octets zero-padded), extendable with connection times (`-t`) and Ethernet addresses (`-e`). The BlackArch package also installs the companion column aggregator `ipaudit-total`.

## Typical use cases

### Summarize a fixed number of live packets

Snapshot the traffic matrix of an interface.

```
sudo ipaudit -c 10000 eth0
```

Outcome: reads 10,000 packets from `eth0` in promiscuous mode, then prints one line per connection with byte/packet totals for both directions to stdout; use `-o file` to write the summary to a file instead.

### Tally traffic from an existing capture

Post-process a pcap instead of sniffing live.

```
ipaudit -r capture.pcap -t -e -o traffic.txt
```

Outcome: reads `capture.pcap` (any tcpdump/ethereal-style dump) and writes per-connection totals to `traffic.txt`, each line extended with connection start/stop times and both hosts' MAC addresses.

### Filter what gets counted

Apply a tcpdump-style BPF filter before tallying.

```
sudo ipaudit -f "host 198.51.100.10" eth0
```

Outcome: only packets involving `198.51.100.10` are read and tallied; the filter string is passed to the pcap library, so the full tcpdump filter syntax applies (for VLAN-tagged traffic prefix the filter with `vlan`).

### Feed a SQL database

Turn tallies into INSERT statements for storage and graphing.

```
sudo ipaudit -q eth0 | mysql -u user -p ipaudit
```

Outcome: instead of columnar output, emits one INSERT statement per connection (fields ip1, ip2, protocol, ports, byte and packet counters) matching the documented `ipaudit` table schema; adding `-D60` repeats the dump every 60 seconds for continuous logging.

### Subtotal the summary by protocol

Aggregate ipaudit's columnar output with the companion tool.

```
ipaudit-total 3 6 traffic.txt
```

Outcome: groups `traffic.txt` by column 3 (protocol) and prints the sum of column 6 (host-1 bytes) for each group — the same key/data-column grouping the manpage demonstrates for summing or averaging any numeric column.

*No corpus evidence — derived from upstream documentation.*
