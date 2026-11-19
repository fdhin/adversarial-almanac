---
name: "mylg"
categories:
  - blackarch-networking
version: "659.faba867"
homepage: "https://github.com/mehrdadrad/mylg"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-27"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "mehrdadrad/mylg"
github-stars: 2719
github-pushed: "2020-02-26"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mylg

> Mehrdad Rad's all-in-one command-line network diagnostics shell — ping, real-time traceroute, packet capture, port scanning, WHOIS/RIPE/PeeringDB lookups and looking-glass BGP queries behind one `local>` prompt.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Diagnosing network problems from a single interactive prompt instead of a dozen single-purpose tools. Inside the shell you get local ping (v4/v6), real-time traceroute with per-hop ASN annotation, a tcpdump-style packet analyzer with filter expressions, TCP port scanning, LAN discovery, HTTP/HTTPS request timing ("hping"), WHOIS for ASNs and prefixes, RIPE and PeeringDB data, DNS queries against servers across 200+ countries, remote looking-glass BGP route queries (Telia, Level3, NTT, Cogent, KPN), a quick NMS mode for polling router interfaces, and a web dashboard. Every command also runs one-shot from the OS shell (`mylg ping example.com`). Written in Go on libpcap — capture, scan and discovery commands need root. Upstream activity stopped in 2020, but the toolset it folds together is timeless.

## Typical use cases

### Open the interactive shell

```
mylg
```

Outcome: prints the myLG banner and drops you at the `local>` prompt with tab completion, history and vi/emacs modes; any probe command (ping, trace, dump, scan, whois, ...) runs from here, and `show config` / `set <probe> <key> <value>` adjust defaults (e.g. `set hping count 10`).

### Run a single probe without entering the shell

```
mylg ping example.com
```

Outcome: executes the command directly from the OS shell and exits with its output — every in-shell command works this way (e.g. `mylg peering 15169` prints the PeeringDB record for AS15169 and its exchange points).

### Capture live packets with a filter

```
dump tcp and port 443 -c 10
```

Outcome: (in-shell, needs root) prints the next ten TCP/443 packets as one-line summaries like `23:26:56.026 IPv4/TCP 192.168.0.104:64686 > 192.0.80.242:443(https) [F.], win 8192, len: 0`; `-x` adds a hex dump of the payload, `dump -d` lists local interfaces and addresses, and negation filters (`dump !udp`) combine freely.

### Save matching packets to a pcap file

```
dump !udp -w /tmp/mypcap -c 100000
```

Outcome: silently collects up to 100000 non-UDP packets into /tmp/mypcap in pcap format for later analysis in Wireshark or tcpdump.

### Trace a route with ASN annotation

```
trace example.com
```

Outcome: real-time traceroute where each hop shows reverse DNS and the ASN that owns it (e.g. `[ASN 15169/GOOGLE]`), making transit hand-offs visible directly in the trace output.

### Scan ports on a host

```
scan target.example.com -p 1-500
```

Outcome: (needs root) TCP-connect scan of ports 1-500 rendered as a table of protocol/port/status rows for open ports, ending with `Scan done: N opened port(s) found in X seconds`; the default range and other knobs live under `set scan`.

### Look up who owns a prefix or ASN

```
whois 8.8.8.8
```

Outcome: prints the covering prefix, origin ASN and holder (e.g. `8.8.8.0/24 | 15169 | GOOGLE - Google Inc., US`); on an AS number (`whois 577`) it returns the owner plus a table of the countries and percentages that AS covers.

### Time HTTP requests to a site

```
hping https://example.com -c 4
```

Outcome: issues four HTTP requests (HEAD by default) and reports per-request status, DNS lookup, connect and first-byte times, then `HTTP Round-trip min/avg/max` and the response-code distribution — a quick latency profile without a browser.
