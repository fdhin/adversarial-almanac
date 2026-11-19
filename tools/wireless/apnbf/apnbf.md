---
name: "apnbf"
categories:
  - blackarch-wireless
version: "0.1"
homepage: "http://www.c0decafe.de/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/apnbf"
github-match: "search"
github-stars: 0
github-pushed: "2019-09-06"
github-archived: false
github-issues: 0
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# apnbf

> Brute-forces APN (Access Point Name) candidates against a GTP-C node to discover which APNs are actually provisioned.

## What it's for

apnbf is a small Python 2 script (Daniel Mende, 2011) for testing the control plane of GPRS/UMTS core networks: for every APN in a wordlist it sends a hand-crafted GTP-C "Create PDP Context Request" (UDP port 2123) to a GGSN/SGSN you name, then classifies the responses by their GTP cause code — cause 128 means the request was accepted, i.e. the APN exists and is allowed. It is for auditing mobile-core test setups (validating APN whitelists, finding misconfigured or forgotten APNs); it does not touch 802.11 at all and cannot brute-force anything Wi-Fi related. The only tunables are the wordlist, the delay between attempts, and verbosity. The c0decafe.de release has no source repository of its own; it is mirrored on GitHub at Seabreg/apnbf, whose `apnbf.py` and wordlist are byte-identical to the upstream 0.1 tarball.

## Typical use cases

### Enumerate valid APNs with the shipped wordlist

Probe a lab GGSN for every APN in the packaged list.

```
apnbf -w /usr/share/apnbf/apnlist -d 1 198.51.100.10
```

Outcome: prints `trying <apn>` for each candidate, and for each APN the node accepts prints `*** APN FOUND: <apn>`; rejected candidates surface as GTP cause codes such as `Missing or unknown APN` (219) or `APN access denied - no subscription` (222), which distinguishes unknown from blocked APNs.

### Fast custom-wordlist run with response decode

Use your own APN list, no delay, and verbose decoding of every reply.

```
apnbf -w apns.txt -d 0 -v 198.51.100.10
```

Outcome: `-v` adds a per-response decode line (version, message type, length, sequence, cause) from the answering node, so the run doubles as a quick check of which node answers and how it classifies each request; `-d 0` removes the default one-second spacing between probes.

*No corpus evidence — derived from upstream documentation.*
