---
name: "issniff"
categories:
  - blackarch-sniffer
version: "294.79c6c2a"
homepage: "https://github.com/juphoff/issniff"
packaged-by: [blackarch]
blackarch-builddate: "2017-02-18"
build-system: autotools
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "juphoff/issniff"
github-stars: 2
github-pushed: "2024-03-06"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# issniff

> Session-level TCP sniffer: collects the full data of connections to chosen ports and dumps each session when it ends.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Capturing everything that flows over TCP connections to ports you name, then printing each session's payload together with its statistics. It puts an interface into promiscuous mode, watches for the SYN that opens a connection to a selected destination port, accumulates the data, and on FIN, RST, timeout or a size cap prints the collected bytes plus hosts, ports, timestamps, packet/byte counts and the reason collection stopped. It is a mid-1990s tool for cleartext-era networks — telnet, FTP, pre-SSL HTTP — so expect no TLS handling and no decryption; it is purely a session dumper. Input can come from a live interface, a `tcpdump -w` file, or a pipe fed by tcpdump.

## Typical use cases

### Capture full telnet sessions in both directions

```
issniff -i eth0 -v +23
```

Outcome: eth0 is switched to promiscuous mode and each TCP connection involving port 23 is tracked in both directions (the `+` prefix); when a session closes its complete payload and a statistics header are printed to stdout, while `-v` also announces each newly detected connection on stderr as it starts.

### Dump HTTP client requests from a tcpdump capture

```
issniff -w sessions.pcap 80
```

Outcome: reads the `tcpdump -w` file instead of a live interface and prints the client-to-server data of every captured connection to port 80 (URLs, headers, basic-auth strings) with per-session statistics.

### Feed live filtered traffic through a pipe

```
tcpdump -i eth0 -w - tcp | issniff -w - 21 23
```

Outcome: tcpdump does the capturing (so its full filter syntax applies) and streams the packets to issniff via stdin, which dumps each FTP-control or telnet session as it completes in the stream.

### Cap how much a single session can collect

```
issniff -i eth0 -d 4096 -t 120 +23
```

Outcome: a session is dumped once 4 kB of data has been collected or after 120 seconds of idle, whichever comes first, instead of waiting for the connection to close — keeps long-lived sessions from filling memory.
