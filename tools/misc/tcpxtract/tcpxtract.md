---
name: "tcpxtract"
categories:
  - blackarch-misc
version: "1.0.1"
homepage: "http://tcpxtract.sourceforge.net"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-02-01"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "Seabreg/tcpxtract"
github-stars: 1
github-pushed: "2019-06-17"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tcpxtract

> Carves files out of TCP streams — from a live interface or a pcap capture — by matching their byte signatures.

## What it's for

tcpxtract reassembles TCP sessions from sniffed traffic and extracts embedded files (images, documents, archives, and anything else recognizable) by matching start/stop byte signatures listed in its config file (`/etc/tcpxtract.conf` by default, editable to add your own file types). It reads either a saved capture or a live interface, making it a quick forensic triage tool for "what files crossed this link" during incident response or monitoring exercises. It matches signatures only — it is not a protocol dissector, does not reassemble TLS-encrypted content, and only recovers file types present in its signature list.

## Typical use cases

### Carve files from a packet capture

Recover the documents and images inside a saved pcap.

```
tcpxtract -f capture.pcap
```

Outcome: every recognized file is reassembled from the capture's TCP streams and dumped into the current directory, each extraction reported as it completes.

### Extract from live traffic

Watch an interface and pull files out of sessions as they happen.

```
tcpxtract -d eth0
```

Outcome: the interface is captured live; matching files are carved and written to the current directory until the process is interrupted.

### Use a custom signature list and output directory

Control what gets detected and where it lands — for example after adding your own file signatures to the config.

```
tcpxtract -f capture.pcap -c /etc/tcpxtract.conf -o carved/
```

Outcome: extraction runs with the signature set from the named config file, and all carved files are written under `carved/` instead of the working directory.

*No corpus evidence — derived from upstream documentation.*
