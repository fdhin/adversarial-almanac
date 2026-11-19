---
name: "hdcp-genkey"
categories:
  - blackarch-crypto
version: "18.e8d342d"
homepage: "https://github.com/rjw57/hdcp-genkey"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "rjw57/hdcp-genkey"
github-stars: 164
github-pushed: "2021-10-07"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hdcp-genkey

> Derives HDCP device keys (source or sink) from the leaked HDCP master key.

## What it's for

A compact Python reference implementation of the HDCP v1.x key-generation algorithm. After the HDCP master key leaked in 2010, anyone can compute valid per-device key sets; this script does exactly that — derive a source (transmitter) or sink (display) key set from the master key in `master-key.txt`, with a chosen or random KSV, and optionally verify the pair is mutually consistent. It is a research/demonstration tool for studying the broken HDCP key hierarchy; it does not interact with displays, capture traffic, or strip encryption from content.

## Typical use cases

### Generate a source device key with a random KSV

Produce a fresh transmitter key set in the default human-readable form.

```
hdcp-genkey
```

Outcome: the script loads the master key, picks a random KSV, and prints the derived source key with its KSV — the fields a device would store as its HDCP identity.

### Generate a sink key with a chosen KSV, machine-readable

Derive a receiver key set for a specific KSV and emit it for scripting.

```
hdcp-genkey -k --ksv 54f0af39a8 -j
```

Outcome: `-k` switches to sink-key generation, `--ksv` fixes the 40-bit KSV in hex, and `-j` prints the key and KSV as a JSON object ready to feed into other tooling.

### Self-test the key-generation math

Confirm the implementation derives mutually consistent source/sink pairs.

```
hdcp-genkey -t
```

Outcome: generates both a source and a sink key and verifies the pair works together, reporting success or failure — a quick integrity check after porting the master key file (`-m FILE` loads a master key from a non-default location).

*No corpus evidence — derived from upstream documentation.*
