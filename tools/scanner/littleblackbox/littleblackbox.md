---
name: "littleblackbox"
categories:
  - blackarch-scanner
version: "0.1.3"
homepage: "http://code.google.com/p/littleblackbox/wiki/FAQ"
packaged-by: [blackarch]
blackarch-builddate: "2014-06-08"
build-system: autotools
license: MIT
meta-checked: "2026-11-09"
github-repo: "devttys0/littleblackbox"
github-stars: 274
github-pushed: "2024-02-09"
github-archived: false
github-issues: 5
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# littleblackbox

> Match embedded-device TLS certificates against a shipped database of extracted private keys.

## What it's for

LittleBlackBox (Craig Heffner) ships a database (`lbb.db`) of private SSL keys extracted from embedded devices — routers, printers, IoT firmware — together with a query tool. Given a live host, a packet capture, or a certificate file, it tells you whether the certificate being presented belongs to a device family whose private key is already public, i.e. whether that host's TLS can be decrypted or impersonated. The database is also searchable by hardware or firmware vendor. It reflects the 2008–2013 device era, so treat it as a legacy-firmware tool rather than coverage of current products.

## Typical use cases

### Check a live host

Test a device directly over the network.

```
littleblackbox --host=198.51.100.10:443
```

Outcome: connects, pulls the server certificate, and compares its fingerprint against `lbb.db`; a match prints the device description and the corresponding known private key.

### Scan captured traffic offline

Work from a capture instead of touching the host.

```
littleblackbox --pcap=capture.pcap
```

Outcome: parses SSL certificate exchanges inside the pcap file and reports any whose private key exists in the database.

### Match a certificate you already hold

Check a exported cert without any network access.

```
littleblackbox --pem=cert.pem
```

Outcome: compares the local PEM certificate against the database and prints any matching key and device record.

### Find keys by device or firmware

Pull candidates before the exact model is known.

```
littleblackbox --search=hardware.vendor=linksys
```

Outcome: lists database entries for that vendor — valid search keys follow the bundled sqlite schema (`hardware.vendor`, `firmware.vendor`, etc.).
