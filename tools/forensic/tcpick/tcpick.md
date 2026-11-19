---
name: "tcpick"
categories:
  - forensics
version: "0.2.1-12"
homepage: "http://tcpick.sourceforge.net"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali, aur]
build-system: make
meta-checked: "2026-11-09"
github-repo: "athomason/tcpick"
github-stars: 21
github-pushed: "2012-01-12"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tcpick

> Text-mode TCP stream sniffer and connection tracker.

## What it's for

tcpick is a libpcap-based, terminal-only sniffer that tracks TCP connections and either displays their payload on screen (printable, hexdump, or raw modes) or rebuilds each connection into per-connection files. It is built for passively picking up transferred data — files, HTTP requests, chat — with output designed to pipe through grep, sed, and awk. It handles only TCP and never decrypts anything; it can replay captures written by `tcpdump -w` but does not replace a full protocol analyzer.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Watch payload data live and filter it

See the content of packets as they cross the wire.

```
tcpick -i eth0 -yP | grep GET
```

Outcome: prints each sniffed packet's payload with non-printable bytes shown as dots (`-yP`), keeping text protocols readable; piping through grep isolates lines of interest such as HTTP GET requests.

### Rebuild whole connections to files

Save fully reassembled stream data per connection.

```
tcpick -i eth0 -wRu "port 80"
```

Outcome: writes rebuilt raw stream data (`-wR`) for each matching connection; `u` merges both directions into a single file named `<ip_client>_<ip_server>_<port>.tcpick`, and adding `b` (`-wRub`) writes [client|server] offset banners between the segments.

### Replay a saved tcpdump capture

Analyze an offline capture with no interface or privileges.

```
tcpick -r capture.pcap -yX
```

Outcome: reads the pcap written by `tcpdump -w` and displays every payload as a hex-and-ASCII dump (`-yX`; use `-yP` for the printable view instead).

### Limit tracking on busy links

Keep the tool responsive on high-traffic interfaces.

```
tcpick -i eth0 -Tf 10 -yP
```

Outcome: fully tracks only the first 10 connections and discards the rest (`-Tf`); combine with `-e 1000` to exit cleanly after 1000 packets when only a sample is needed.
