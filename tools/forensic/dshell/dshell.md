---
name: "dshell"
categories:
  - blackarch-forensic
version: "142.695c891"
homepage: "https://github.com/USArmyResearchLab/Dshell"
packaged-by: [blackarch]
blackarch-builddate: "2019-07-27"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "USArmyResearchLab/Dshell"
github-stars: 5509
github-pushed: "2026-10-29"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# dshell

> An extensible network-forensic analysis framework: dissect packet captures with chainable decoder plugins from an interactive shell.

## What it's for

Dshell (U.S. Army Research Laboratory) is a framework for investigating
network packet captures. You launch `dshell` to get a `Dshell>` prompt
(all `decode ...` commands below are entered there) and run its `decode`
command to apply a decoder plugin to a pcap or pcapng file, or to a live
interface. It handles stream reassembly and IPv4/IPv6, lets you chain
plugins (for example a GeoIP annotator feeding a flow summarizer), and
supports pluggable output formats such as JSON or writing the matched
packets to a new pcap. It analyzes captures you already have — it is not
a collector — and what you can see is bounded by the available plugins,
though writing your own in Python is a supported workflow.

## Typical use cases

### Survey the available decoder plugins

See which decoders are installed before picking one.

```
decode -l
```

Outcome: a list of every plugin with its name and a one-line
description; `decode -p <plugin>` shows the full description and the
plugin-specific command-line flags of one plugin.

### Dissect DNS traffic in a capture

Run a protocol decoder over a capture file.

```
decode -p dns capture.pcap
```

Outcome: one line per DNS transaction with timestamp, endpoints, query
type and name, and the answers returned — a quick inventory of lookups
and exfil-style TXT usage.

### Reassemble and read a TCP stream

Follow a conversation instead of single packets.

```
decode -p followstream capture.pcap
```

Outcome: each TCP connection is reassembled and printed as the
back-and-forth byte stream (headers and payloads), so you can read the
actual HTTP/SMTP-style dialogue.

### Chain plugins to filter flows by country

Combine decoders where one annotates and the other summarizes.

```
decode -p country+netflow --country_code=JP capture.pcap
```

Outcome: flow records (endpoints, packets, bytes, duration) annotated
with GeoIP country codes and filtered to traffic involving JP — plugin
options are passed with `--option=value` after the chained plugin list.

### Emit JSON for downstream tooling

Machine-readable output instead of formatted text.

```
decode -p tftp -O jsonout capture.pcap
```

Outcome: one JSON object per event on stdout (timestamp, endpoints,
plugin-specific fields), ready to pipe into `jq` or a log pipeline.

### Carve matching packets into a new pcap

Write selected traffic out for another tool.

```
decode -p dns+pcapwriter --pcapwriter_outfile=dns-only.pcap capture.pcap
```

Outcome: a new pcap containing only the packets the dns plugin matched,
openable in Wireshark or readable by `tcpdump -nnr dns-only.pcap`.

### Watch an interface live

Apply a decoder to traffic as it happens.

```
decode -p netflow -i eth0
```

Outcome: the decoder runs against live traffic on eth0 (may require
superuser privileges) and prints flow summaries until interrupted.

*No corpus evidence — derived from upstream documentation.*
