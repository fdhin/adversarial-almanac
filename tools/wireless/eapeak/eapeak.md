---
name: "eapeak"
categories:
  - blackarch-wireless
version: "130.9550d1c"
homepage: "https://github.com/securestate/eapeak"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2, GPL3
meta-checked: "2026-11-09"
github-repo: "securestate/eapeak"
github-stars: 129
github-pushed: "2019-02-04"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# eapeak

> Passive and active enumeration of EAP methods on 802.1X wireless networks (SecureState).

## What it's for

EAPeak (Spencer McIntyre, SecureState) audits wireless networks that authenticate with the Extensible Authentication Protocol. Its `eapeak` command passively analyzes EAP traffic — from packet captures or a live monitor interface — profiling which EAP methods and identities a network uses and extracting the server certificates presented during certificate-based EAP exchanges. Its `eapscan` command actively probes a target 802.1X network with crafted EAP messages to enumerate the EAP types it accepts and whether WPS is enabled. Reach for it during reconnaissance of WPA2-Enterprise networks before committing to an attack; it gathers information only — it does not crack or capture credentials.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Analyze EAP traffic in a packet capture

Profile what a capture of a WPA2-Enterprise network reveals about its authentication setup.

```
eapeak -f capture.pcap -s CorpWifi
```

Outcome: prints a per-network summary of the EAP traffic seen in the capture (methods, identities), restricted to the named SSID; add `--xml` to also export the findings as an XML file.

### Watch EAP networks live

Monitor a monitor-mode interface and get a continuously updated picture of nearby 802.1X networks.

```
eapeak -i wlan0mon -l -c
```

Outcome: live capture on the interface with the curses interface for continuous display; results are saved to XML by default while capturing.

### Extract the authentication server's certificate

Pull the x.509 certificate an EAP server presents so it can be inspected, cloned into a rogue AP setup, or checked against a corporate CA.

```
eapeak -f capture.pcap --export-x509 --x509-format pem
```

Outcome: runs the certificate export wizard and writes certificates recovered from EAP exchanges to PEM files (`--x509-format der` for DER), ready for inspection with openssl.

### Actively enumerate a target 802.1X network

Probe an access point directly to learn which EAP methods it accepts, without waiting for a legitimate client to authenticate.

```
eapscan -e CorpWifi -b AA:BB:CC:DD:EE:FF -i wlan0mon -c 6 --check-wps
```

Outcome: sends EAP probes to the target AP and reports which EAP types it offers and whether WPS is enabled; `--all` extends the sweep across EAP types 4-254 and `--identity` overrides the outer identity string used.
