---
name: "pskracker"
categories:
  - cracker
version: "0.3.1+git20230102-0parrot1"
homepage: "https://github.com/soxrok2212/PSKracker"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "soxrok2212/PSKracker"
github-stars: 452
github-pushed: "2023-08-31"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pskracker

> Generates default WPA/WPA2 keys and WPS PINs for supported router and gateway models.

## What it's for

PSKracker is a collection of per-model algorithms that compute the factory-default WPA/WPA2 keys, guest keys and WPS PINs shipped by wireless routers and ISP gateways (Arris NVG589/NVG599, Comcast Xfinity, Belkin, and others). Where a default key is derived deterministically from public values like the BSSID or the device serial number, pskracker reconstructs it instantly instead of brute-forcing the keyspace — often printing one exact key, and otherwise a small candidate set to feed a handshake cracker. Reach for it after capturing a handshake from a network whose hardware still runs vendor defaults; it does not attack WPA cryptography itself and does nothing for access points with user-chosen passwords.

## Typical use cases

### Generate all default-key candidates for a model

Enumerate every key the model's algorithm can produce when no device-specific input is known.

```
pskracker -t nvg599
```

Outcome: streams the model's full candidate space of default keys/PINs to stdout; stop it early or capture it to a file — useful as a small model-specific wordlist.

### Derive the key for one specific device

Reconstruct the single expected default key from the device's wireless MAC.

```
pskracker -t dpc3941 -b aabbccddeeff
```

Outcome: prints the deterministic default key for that BSSID (e.g. `PSK: 5756C3915966657704`) — try it directly against the captured handshake before any cracking.

### Restrict output to WPS PINs or guest keys

Split the candidate stream when only one network (main, guest, or WPS) is in scope.

```
pskracker -t nvg599 -W
pskracker -t dpc3941 -b aabbccddeeff -G
```

Outcome: `-W` prints only possible WPS PIN(s), `-G` only possible guest-network WPA key(s) — smaller candidate sets to try against the respective network.

### Save candidates as a wordlist for a handshake cracker

Turn the generated keyspace into a file a standard cracker can consume.

```
pskracker -t nvg599 > candidates.txt
```

Outcome: `candidates.txt` holds one candidate per line, ready to pass to a handshake cracker via its wordlist option; if the handshake's key is a vendor default it resolves in seconds.

*No corpus evidence — derived from upstream documentation.*
