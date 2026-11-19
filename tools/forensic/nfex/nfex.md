---
name: "nfex"
categories:
  - blackarch-forensic
version: "2.5"
homepage: "https://code.google.com/p/nfex/"
packaged-by: [blackarch]
blackarch-builddate: "2015-02-07"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "mschiffm/nfex"
github-stars: 11
github-pushed: "2014-02-27"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nfex

> Carve files out of network traffic — live from an interface or offline from a pcap — using magic-byte signatures in a tcpxtract-style config.

## What it's for

nfex (Network File Extractor, by Nicholas Harbour and Mike Schiffman of Cisco) reassembles TCP sessions from a capture and extracts the files transferred inside them, either in real time from a live network device or after the fact from a libpcap (tcpdump) savefile. Harbour is the author of tcpxtract, and nfex reuses its signature configuration format: one line per file type, `name(minsize, magic-bytes);`, such as `exe(10000000, \x4d\x5a\x90\x00);` — the number is the minimum size in bytes and the escaped bytes are the magic the extractor anchors on. A trailing BPF filter expression (tcpdump syntax) narrows the traffic processed. Each carved file lands in the output directory as `<pid>-<seq>.<type>`, and a `<pid>-index.txt` maps every file to the capture source, an ISO-8601 timestamp and the source/destination IP and port pair — the trail back from a carved artifact to the conversation that carried it.

## Typical use cases

### Carve transferred files from a saved capture

Signature-driven extraction over an offline pcap.

```
nfex -v -c signatures.conf -f capture.pcap -o carved
```

Outcome: every file matching a signature in `signatures.conf` is reassembled into `carved/` named `<pid>-<seq>.<ext>`, and `carved/<pid>-index.txt` records for each one the capture file, timestamp and src/dst address.port so files can be tied back to the session that carried them.

### Extract files from live traffic

Run against an interface, optionally restricted by a BPF filter.

```
sudo nfex -v -c signatures.conf -d eth0 -o live-out tcp port 80
```

Outcome: matching files are extracted and indexed as they pass the interface; the trailing expression filters exactly like a tcpdump filter, so only the named traffic is processed. Stop the capture with Ctrl-C when done.

### Tune what gets carved

The config file decides the extraction set, not the command line.

```
printf 'exe(10000000, \\x4d\\x5a\\x90\\x00);\npdf(1000000, \\x25\\x50\\x44\\x46);\n' > signatures.conf
```

Outcome: a two-signature config that carves MZ executables and PDFs only; add or trim `name(minsize, magic);` lines to widen or narrow the sweep, then pass the file with `-c`.

*No corpus evidence — derived from upstream documentation.*
