---
name: "tcptrace"
categories:
  - blackarch-networking
version: "6.6.7"
homepage: "http://tcptrace.org/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-12-06"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "blitz/tcptrace"
github-stars: 80
github-pushed: "2017-04-05"
github-archived: true
github-issues: 8
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tcptrace

> Classic TCP dump-file analyzer by Shawn Ostermann (Ohio University) — per-connection statistics on throughput, RTT, retransmissions and windowing, plus time-sequence graphs.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Post-mortem analysis of TCP behavior from a packet capture: how many connections are in the file, who talked to whom, how many bytes and retransmissions each direction carried, what the round-trip times looked like, and whether windows or congestion limited throughput. You feed it one or more dump files (compressed files and `stdin` are accepted) and it prints a summary plus one statistics block per connection; graphing flags produce xplot files, and the bundled `xpl2gpl` converts those to gnuplot. It analyzes existing captures — it does not capture traffic itself (tcpdump or similar provides the input), and its focus is TCP; UDP gets only minimal analysis.

## Typical use cases

### Summarize all connections in a capture

```
tcptrace -n capture.pcap
```

Outcome: prints `N packets seen, M TCP packets traced` and one brief block per connection (host a/b addresses and ports, completeness, duration, packets and bytes per direction). `-n` skips reverse DNS, which is much faster on large files.

### Full statistics for each connection

```
tcptrace -l -n capture.pcap
```

Outcome: the long output format — each connection block expands to segment-size statistics (min/max/avg), SYN/FIN counts, advertised windows and full duplex counters for both directions, giving the material for a per-session health check.

### Round-trip time statistics

```
tcptrace -r capture.pcap
```

Outcome: adds RTT statistics per connection (min/max/avg round-trip times per direction). Slower on large files, so run it on a targeted capture rather than a multi-gigabyte one.

### Extract the payload of each TCP stream

```
tcptrace -e capture.pcap
```

Outcome: writes the data bytes of each TCP stream to files in the current directory, one per connection direction — a quick way to pull reassembled payloads out of a capture.

### Generate time-sequence graphs

```
tcptrace -S capture.pcap
```

Outcome: produces time-sequence graph files (xplot format) for each connection; view them with `xplot`, or convert to gnuplot data with the bundled `xpl2gpl`. `-G` generates all graph types (throughput, RTT samples, outstanding data, segment size, timeline) at once.
