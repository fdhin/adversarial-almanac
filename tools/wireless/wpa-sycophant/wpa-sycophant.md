---
name: "wpa-sycophant"
categories:
  - wireless
version: "1.0+git20210103-0parrot1"
homepage: "https://github.com/sensepost/wpa_sycophant"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: make
meta-checked: "2026-11-09"
github-repo: "sensepost/wpa_sycophant"
github-stars: 224
github-pushed: "2026-10-07"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wpa-sycophant

> Relays captured PEAP/MSCHAPv2 phase-2 authentication attempts onto the real corporate WLAN — companion tool to hostapd-mana (SensePost).

## What it's for

WPA_Sycophant (SensePost, released at DEF CON 26) is the relay half of an MSCHAPv2 attack against WPA/WPA2-Enterprise networks: the other half, [[hostapd-mana]] run with `enable_sycophant`, operates a rogue AP that captures the first part of a PEAP client's challenge-response. Sycophant itself is a modified wpa_supplicant that then replays that captured authentication attempt against the legitimate corporate SSID, so the operator's machine completes the EAP authentication and joins the corporate wireless network without ever knowing the user's password. Current support covers PEAP with MSCHAPv2. It is not a cracking tool — nothing is brute-forced; the victim's own live authentication is relayed, so the technique needs the rogue AP and the relay supplicant running together.

## Typical use cases

### Run the relay supplicant against a target SSID

With hostapd-mana already capturing victims nearby, run Sycophant on a second interface.

```
sudo wpa_sycophant.sh -c wpa_sycophant_example.conf -i wlan1
```

Outcome: the modified wpa_supplicant loads the target network definition from the config file (`ssid`, `key_mgmt=WPA-EAP`, `eap=PEAP`, `phase2="auth=MSCHAPV2"`), associates to the corporate SSID and relays the MANA-captured challenge-response; on success the interface is authenticated onto the corporate WLAN.

### Prevent relaying to your own rogue AP

Blacklist the rogue AP's BSSID in the config so the supplicant never loops back to it.

```
network={
  ssid="ExampleCorp"
  scan_ssid=1
  key_mgmt=WPA-EAP
  eap=PEAP
  phase1="crypto_binding=0 peapver=0"
  phase2="auth=MSCHAPV2"
  bssid_blacklist=00:14:22:01:23:45
}
```

Outcome: when Sycophant scans for the target SSID it ignores the blacklisted BSSID (your own MANA access point) and only authenticates toward the legitimate corporate APs; without this, the relay can authenticate to itself and the attack silently fails.

*No corpus evidence — derived from upstream documentation.*
