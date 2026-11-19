---
name: "crackq"
categories:
  - blackarch-cracker
version: "48.89b7318"
homepage: "https://github.com/vnik5287/Crackq"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "vnik5287/Crackq"
github-stars: 85
github-pushed: "2016-03-27"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# crackq

> Command-line client (crackqcli) for the hashcrack.org online distributed GPU cracking service.

## What it's for

Submits hashes to Crackq, a hosted distributed GPU cracking service at hashcrack.org, so remote GPUs do the work and results are emailed to the account holder. Supported types are md5, ntlm, sha1, wpa (single-ESSID .hccap files), md5crypt, descrypt, pdf (v1.4–1.6), phpass and mysql 4.1+. A hashcrack.org API key is required — it is stored in `~/.crackq` and prompted for on the first run — and each submission draws down the account's quota. This is a 2016-era Python 2 client tied to that specific service; it performs no local cracking.

## Typical use cases

### Submit a single NTLM hash

Hand one hash to the service.

```
crackqcli -t ntlm 5f4dcc3b5aa765d61d8327deb882cf99
```

Outcome: validates the 32-hex-digit hash format, prints the account email and remaining submissions, then queues the hash; the cracked plaintext is emailed to the account address.

### Submit a WPA handshake file

Send a captured handshake for GPU attack.

```
crackqcli -t wpa capture.hccap
```

Outcome: reads the 392-byte single-ESSID hccap file (rejecting files that are invalid or contain multiple ESSIDs), compresses and uploads it, and queues it for cracking.

*No corpus evidence — derived from upstream documentation.*
