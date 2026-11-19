---
name: "ubiquiti-probing"
categories:
  - blackarch-recon
version: "5.c28f4c1"
homepage: "https://github.com/headlesszeke/ubiquiti-probing"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "headlesszeke/ubiquiti-probing"
github-stars: 19
github-pushed: "2016-09-29"
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

# ubiquiti-probing

> Discover Ubiquiti devices on the local segment by sending the four-byte discovery probe to UDP/10001 and parsing the descriptive replies.

## What it's for

A minimal Ruby script (scan.rb) that exploits Ubiquiti's discovery protocol: any Ubiquiti device listening on UDP port 10001 answers a four-byte request (`\x01\x00\x00\x00`) with a self-describing payload. The script broadcasts the probe to 255.255.255.255:10001, waits five seconds, and parses every reply into MAC address, IP, hostname, product, firmware version, uptime and any unrecognized TLV fields. It is pure discovery — read-only, one packet, no attack surface touched — and was authored against 2016-era AirCam hardware, so newer UniFi gear may answer with additional or differently-ordered fields the parser dumps as raw hex (still evidence, just less labeled). Useful during physical/lab recon to inventory Ubiquiti cameras, radios and APs on a segment you are attached to.

## Typical use cases

### Sweep the local segment for Ubiquiti gear

Run the broadcast discovery from a machine on the target LAN.

```
ubiquiti-probing
```

Outcome: sends the discovery request to the broadcast address on UDP/10001, waits 5 seconds, then prints one block per responding device — MAC, IP, HOSTNAME, PRODUCT, VERSION, UPTIME plus a hexdump of unknown fields — giving an instant inventory of Ubiquiti hardware and firmware versions for follow-up (default-credential checks, version-matched CVE lookup).

### Run the script directly from a checkout

Invoke the Ruby source without the packaged wrapper.

```
ruby scan.rb
```

Outcome: identical discovery run from a source checkout; the broadcast address (255.255.255.255), bind address and port (10001) are constants at the top of scan.rb — edit them there to target a specific subnet broadcast or a unicast address instead of the global broadcast.

*No corpus evidence — derived from upstream documentation.*
