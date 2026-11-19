---
name: "cameradar"
categories:
  - blackarch-scanner
version: "205.affbdd2"
homepage: "https://github.com/Ullaakut/cameradar"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2025-11-23"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "Ullaakut/cameradar"
github-stars: 5291
github-pushed: "2026-10-27"
github-archived: false
github-issues: 21
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 87
gh-activity-checked: "2026-11-09"
---

# cameradar

> RTSP toolkit that discovers cameras on a network and dictionary-attacks their stream routes and credentials.

## What it's for

Cameradar (by Ullaakut) scans authorized networks for RTSP endpoints (default ports 554, 5554, 8554, 322, 8322), identifies the camera model behind each stream, then runs dictionary attacks over the bundled — or your own — route and credential lists to recover stream access, and reports every finding, optionally as an M3U playlist openable in VLC (`rtsp://user:pass@host:port/route`). Discovery runs through nmap by default or masscan (`--scanner masscan`) for very large networks, and `--skip-scan` attacks a known inventory directly. It is an offensive testing tool: point it only at networks and devices you are explicitly permitted to test.

## Typical use cases

### Sweep a subnet for cameras

```
cameradar --targets 198.51.100.0/24
```

Outcome: discovers RTSP hosts on the default ports, identifies their models, attacks routes and credentials with the bundled dictionaries, and prints a findings report; targets accept IPs, ranges (`198.51.100.10-20`), hostnames, CIDRs, or a newline-separated file path.

### Custom dictionaries on specific ports

```
cameradar --targets 198.51.100.10 --ports "554,8554" --custom-routes routes.txt --custom-credentials credentials.json
```

Outcome: attacks only the listed ports, using your route list and credentials JSON in place of the bundled dictionaries.

### Attack a known endpoint without discovery

```
cameradar --skip-scan --ports "554,8554" --targets 198.51.100.10
```

Outcome: skips port discovery entirely and treats each target:port as a stream candidate — useful on restricted networks or fixed inventories (no model detection in this mode).

### Validate hits with frame checking

```
cameradar --targets 198.51.100.0/24 --ports "554,8554" --framecheck
```

Outcome: confirms every `200 OK` by attempting playback and waiting for an RTP packet, discarding false positives from cameras with sloppy RTSP behavior at the cost of a longer run; `--output playlist.m3u` writes the working streams as an M3U playlist.

*No corpus evidence — derived from upstream documentation.*
