---
name: "creds"
categories:
  - blackarch-sniffer
version: "17.1ec8297"
homepage: "https://github.com/DanMcInerney/creds.py"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom
aliases: ["creds.py-git"]
meta-checked: "2026-11-09"
github-repo: "DanMcInerney/creds.py"
github-stars: 168
github-pushed: "2015-02-18"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# creds

> Passively harvests cleartext credentials and related activity from FTP, HTTP, POP/IMAP and IRC traffic in one pass.

## What it's for

creds.py (Dan McInerney) is a Scapy-based passive sniffer that reassembles fragmented packets and extracts authentication data from several cleartext protocols simultaneously: FTP, HTTP, POP, IMAP and IRC. Beyond bare credentials it records HTTP searches, visited URLs and POST bodies, To/From/Date/Subject headers of sent or received mail, and IRC channel joins, parts and messages — each tied to the client IP and the server it was talking to. It works live on an interface or offline against a pcap file, and prints a per-IP summary of everything collected on Ctrl-C. It sees only cleartext protocols (TLS traffic is invisible to it), and the same author's later net-creds tool — also in this vault — supersedes it with more parsers.

## Typical use cases

### Sniff credentials on the internet-connected interface

Start capturing without touching any options.

```
sudo python creds.py
```

Outcome: sniffing starts on the interface that `ip route` reports as internet-connected; credentials and protocol activity stream to the console, and Ctrl-C prints a final list of credentials per sniffed IP together with the server each was sent to.

### Limit capture to one interface

Pin the capture when several interfaces are present.

```
sudo python creds.py -i eth0
```

Outcome: only traffic on eth0 is parsed; output is identical to the default run, on a chosen interface (useful on boxes with VPN or Docker interfaces).

### Mine credentials from a saved capture

Work offline against traffic recorded earlier.

```
python creds.py -p capture.pcap
```

Outcome: the pcap file is parsed with the same protocol dissectors as a live run, printing the credentials, URLs, searches, mail headers and IRC activity it contains without touching the network.

*No corpus evidence — derived from upstream documentation.*
