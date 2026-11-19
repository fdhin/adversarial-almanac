---
name: "hostapd-wpe"
categories:
  - blackarch-wireless
version: "2.11"
homepage: "https://w1.fi/hostapd/"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2024-10-10"
build-system: make
license: BSD-3-Clause
meta-checked: "2026-11-09"
github-repo: "OpenSecurityResearch/hostapd-wpe"
github-stars: 527
github-pushed: "2018-06-01"
github-archived: false
github-issues: 21
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hostapd-wpe

> Foundstone's patched hostapd that impersonates an 802.1X Authenticator/Authentication Server to harvest enterprise Wi-Fi credentials.

## What it's for

hostapd-wpe ("Wireless Pwnage Edition", by Brad Antoniewicz of Foundstone) is the replacement for FreeRADIUS-WPE: instead of a separate RADIUS server, the patched hostapd itself impersonates both the 802.1X Authenticator and the Authentication Server. It answers EAP-FAST/MSCHAPv2 (Phase 0), PEAP/MSCHAPv2, EAP-TTLS with MSCHAPv2/MSCHAP/CHAP/PAP, and logs each client's credentials as they flow through; for 802.11 clients it can also send karma-style gratuitous probe responses and, on the cryptography side, launch Heartbleed (CVE-2014-0160) attacks against vulnerable clients. Harvested material goes to stdout and `hostapd-wpe.log`. It is command-line driven via a config file and is the older sibling of SensePost's [[hostapd-mana]] (which adds the full karma state machine and hccapx output; [[berate_ap]] orchestrates mana in one command). Post-2018 upstream is quiet — newer distributions patch current hostapd releases with it.

## Typical use cases

### Stand up the rogue 802.1X AP and harvest credentials

```
sudo hostapd-wpe hostapd-wpe.conf
```

Outcome: starts the impersonating access point defined by `hostapd-wpe.conf` (interface, SSID, WPA-EAP settings; the config template is created at build time). When an enterprise client connects, its username and MSCHAP challenge/response (or cleartext password for PAP/CHAP modes) are printed to the console and appended to `hostapd-wpe.log` — crack the netntlm pairs offline with john or hashcat.

### Answer all probes and keep clients satisfied

```
sudo hostapd-wpe -k -s hostapd-wpe.conf
```

Outcome: `-k` enables karma mode (gratuitous probe responses for any probed SSID, drawing remembered-network clients in) and `-s` makes the server return EAP-Success after harvesting, so clients believe they authenticated and remain connected for follow-on attacks.

### Attack vulnerable TLS clients with Heartbleed

```
sudo hostapd-wpe -c hostapd-wpe.conf
```

Outcome: attempts to exploit CVE-2014-0160 against clients whose EAP-TLS handshake uses a vulnerable OpenSSL — leaking memory (potentially private key material) from the client during association.

### Define which identities the AP accepts

```
grep -v '^\#' hostapd-wpe.eap_user
```

Outcome: shows the EAP user file hostapd-wpe depends on, where each line maps an accepted identity to an EAP method and secret — for example `"CORP\user" MSCHAPV2 "NTHASH" [2]`. Edit this file to match the target environment's usernames and domain; note the file is a plaintext credential store, so on any host actually running hostapd-wpe it is worth reading directly — real appliances have been found shipping their accepted 802.1X identities and secrets in exactly this file.

*Corpus-grounded — the eap_user credential-file pattern mined from corpus evidence (a real appliance deployment shipping the WPE eap_user file); run modes and flags verified against the upstream README.*
