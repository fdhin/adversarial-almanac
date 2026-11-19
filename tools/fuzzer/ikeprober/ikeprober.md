---
name: "ikeprober"
categories:
  - blackarch-fuzzer
version: "1.13"
homepage: "http://ikecrack.sourceforge.net/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ikeprober

> Crafts ISAKMP/IKE initiator packets field by field to probe IPsec VPN gateways for overflows, error paths, and vendor fingerprints.

*No corpus evidence — derived from upstream documentation.*

## What it's for

A single-file Perl tool by Anton T. Rager (2002) that builds IKE initiator packets with nearly every payload element set manually — SA proposals, vendor IDs, nonces, key-exchange data, identifiers, hashes, SPIs — in aggressive or main mode. It is a probing and fuzzing tool: point it at an IPsec/VPN gateway to see how the IKE daemon copes with hand-crafted, malformed, or oversized payloads, which surfaces error conditions, overflows and implementation quirks usable for vendor identification. It only speaks the initiator side and does not crack or verify pre-shared keys.

## Typical use cases

### Probe a gateway with an aggressive-mode SA proposal

Send one hand-crafted initiator with a chosen transform to see how the responder parses it.

```
ikeprober -s 5:2:1:2 -d 198.51.100.10 -rand
```

Outcome: sends an aggressive-mode initiator packet with a 3DES/SHA1/PSK/DH-group-2 proposal (`-s` takes `encr:hash:auth:group` values) and a randomized cookie to the host's UDP/500; the response or silence indicates how the IKE daemon handles the proposal.

### Probe the same gateway in main mode

Compare main-mode and aggressive-mode handling on one host.

```
ikeprober -s 5:2:1:2 -main -d 198.51.100.10
```

Outcome: sends the same crafted initiator as a main-mode packet; differing behavior between the two modes distinguishes how strictly the daemon validates state sequences.

### Stress the SA parser with repeated transforms and payloads

Push oversized payloads at the responder to trigger parser overflows.

```
ikeprober -s 5:2:1:2 -transforms 20 -r 50 -d 198.51.100.10
```

Outcome: builds an SA payload with the transform repeated 20 times (`-transforms`) and the previous payload repeated 50 times (`-r`); watch the responder for crashes, resets, or error responses while it parses the oversized packet.

### Probe with abnormal SA attribute values

Use the built-in SA test cases to separate implementations.

```
ikeprober -sa_test 2 -d 198.51.100.10
```

Outcome: sends an aggressive-mode initiator whose SA carries the `-sa_test 2` attribute set (0xffffffff lifetime; cases 1-4 vary lifetime seconds, group attributes, and TLV attribute size); different vendor stacks reject these at different points, which fingerprints the implementation.
