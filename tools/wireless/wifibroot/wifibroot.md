---
name: "wifibroot"
categories:
  - blackarch-wireless
version: "84.d0cd2cc"
homepage: "https://github.com/hash3liZer/WiFiBroot"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "hash3liZer/WiFiBroot"
github-stars: 1132
github-pushed: "2021-01-15"
github-archived: false
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wifibroot

> Python 2 attack tool for WPA/WPA2: capture handshakes or PMKIDs and crack them, or deauthenticate clients.

## What it's for

WiFiBroot, by hash3liZer, packages scapy into four modes that cover a WPA/WPA2 assessment in one tool: mode 1 captures a 4-way handshake (deauthenticating clients to force reconnection) and cracks the MIC code against a dictionary; mode 2 captures a PMKID from the AP without needing a client and cracks it; mode 3 cracks previously saved captures offline; mode 4 is a deauthentication/jamming attack against APs and stations. It drives the wireless interface through `iwconfig` and generally wants root plus a monitor-mode card with packet injection. It is a Python 2 tool pinned to scapy ≤ 2.4.0, last pushed in 2021 — install python2/scapy accordingly or expect import errors.

## Typical use cases

### Capture and crack a WPA/WPA2 handshake

Sniff for a 4-way handshake on any network and break it with a wordlist.

```
sudo wifibroot --mode 1 -i wlan0mon --verbose -d wordlist.txt -w capture.cap
```

Outcome: listens on the monitor interface (all channels unless filtered), deauthenticates clients to force handshakes, cracks the MIC code against `wordlist.txt`, and writes the captured handshake to `capture.cap`. Add `-b AA:BB:CC:DD:EE:FF`, `-e ESSID` or `-c CHANNEL` to restrict the target; `--deauth N` controls the burst size and `-t` the delay between deauth requests.

### Capture and crack a PMKID

Grab a PMKID straight from an AP with no client attached.

```
sudo wifibroot --mode 2 -i wlan0mon -b AA:BB:CC:DD:EE:FF -d wordlist.txt -w pmkid.txt
```

Outcome: captures the PMKID from the targeted access point, cracks it against the dictionary, and saves the raw PMKID to `pmkid.txt` for later re-use.

### Crack a saved capture offline

Re-run cracking against a previously captured handshake or PMKID without any wireless interface.

```
wifibroot --mode 3 --type handshake --essid "MyNet" -d wordlist.txt -r capture.cap
```

Outcome: reads `capture.cap` and cracks the handshake offline (`--essid` is required for the handshake type). For a PMKID capture use `wifibroot --mode 3 --type pmkid -d wordlist.txt -r pmkid.txt`; `--list-types` prints the supported capture types.

### Deauthenticate clients / jam a network

Disconnect stations continuously as a stress test.

```
sudo wifibroot --mode 4 -i wlan0mon -0 0 --ap AA:BB:CC:DD:EE:FF
```

Outcome: floods deauthentication frames between the given AP and its stations; `-0 0` means unlimited frames. Add `--client AA:BB:CC:DD:EE:FF` to single out one station, or omit both `--ap` and `--client` for an untargeted jam of everything heard on the interface.

*No corpus evidence — derived from upstream documentation (repo README, mode reference and examples).*
