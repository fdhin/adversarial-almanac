---
name: "wpa2-halfhandshake-crack"
categories:
  - blackarch-wireless
version: "29.3f42124"
homepage: "https://github.com/dxa4481/WPA2-HalfHandshake-Crack"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "dxa4481/WPA2-HalfHandshake-Crack"
github-stars: 595
github-pushed: "2022-03-03"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wpa2-halfhandshake-crack

> Dictionary-attacks WPA2 from a half handshake captured against a fake AP — no legitimate AP and no completed 4-way exchange needed.

## What it's for

A proof of concept (by dxa4481) demonstrating that the classic "capture a full handshake" requirement is unnecessary: when an unassociated client probes for an SSID and then attempts to authenticate to a fake access point with that name, the exchange fails — but the first messages of the 4-way handshake already contain enough key material to test passphrase candidates offline. The attack chain is: observe probing clients (e.g. with airodump-ng), stand up a WPA2 AP with the probed SSID and any passphrase, capture the failed exchange, then feed the capture to this tool with the fake AP's MAC and the SSID to run a dictionary attack. It accepts half and full handshakes alike; it cracks the real network's passphrase even though the real AP never participated. The packaged `wpa2-halfhandshake-crack` command wraps the tool's `halfHandshake.py` (Python 2).

## Typical use cases

### Attack a captured half handshake

Run a dictionary attack against a capture taken from your fake AP.

```
wpa2-halfhandshake-crack -r halfhandshake.cap -m 00:11:22:33:44:55 -s "Example-AP" -d passwords.lst
```

Outcome: `-r` reads the capture (half or full handshake), `-m` is the MAC of the fake AP used during the capture and `-s` its SSID; the tool derives the pairwise key material for each candidate in `password.lst` (`-d` selects the dictionary) and reports the passphrase when a candidate matches the captured messages.

### Validate the workflow on the bundled sample

Confirm the tool and workflow against the sample capture shipped with it.

```
wpa2-halfhandshake-crack -r /usr/share/wpa2-halfhandshake-crack/sampleHalfHandshake.cap -m 48d224f0d128 -s "no place like 127.0.0.1"
```

Outcome: runs against the repository's own sample capture with the fake-AP MAC and SSID it was recorded with — a working reference for how the flags map onto a real capture before you use one of your own.

*No corpus evidence — derived from upstream documentation.*
