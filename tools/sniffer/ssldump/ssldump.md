---
name: "ssldump"
categories:
  - blackarch-sniffer
version: "1.7"
homepage: "http://www.rtfm.com/ssldump/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2023-04-19"
build-system: autotools
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "adulau/ssldump"
github-stars: 258
github-pushed: "2026-10-26"
github-archived: false
github-issues: 24
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 91
gh-activity-checked: "2026-11-09"
---

# ssldump

> SSLv3/TLS network protocol analyzer that decodes TLS records and, given keying material, decrypts them.

## What it's for

ssldump, originally written by Eric Rescorla (RTFM, Inc., rtfm.com) and now maintained as the de-facto adulau/ssldump repository (the original 0.9b3 plus distro patches, v1.9+), passively identifies TCP connections carrying SSLv3/TLS and prints a textual, record-by-record decode of each: handshakes, cipher negotiation, certificate chains, and application data. Given the server's private key (-k) or a TLS key log file (-l), it decrypts sessions and shows the plaintext; the maintained version adds JSON output, JA3/JA3S client fingerprints, IPv6, and X.509 certificate extraction. It is purely passive — it does not MITM or generate certificates. Note that forward-secret ciphersuites cannot be decrypted from a server key alone; use -l with key log material for those.

## Typical use cases

### Watch TLS handshakes live

Decode TLS connections as they happen on the local host.

```
ssldump -i any -n
```

Outcome: prints one line per TCP connection and an indented decode of every TLS session seen (ClientHello, ServerHello, certificate chain, chosen cipher); -n skips DNS resolution.

### Emit JSON with JA3 fingerprints

Produce structured output for tooling pipelines.

```
ssldump -j -ANH -n -i any | jq
```

Outcome: one JSON object per event on stdout, including ja3_fp fingerprint values for handshakes — pipe into jq or redirect to a file for further processing or fingerprint lookups.

### Analyze a saved capture offline

Decode TLS from an existing pcap file.

```
ssldump -r capture.pcap
```

Outcome: reads the capture from disk, decodes every TLS session it contains, and exits; no live interface is touched.

### Decrypt with the server's private key

Show plaintext for sessions your RSA server key can decrypt.

```
ssldump -k server.key -r capture.pcap
```

Outcome: RSA-keyed sessions in the capture are decrypted and their application data is printed beneath each record's decode; add -p Password1 when the key file is passphrase-protected.

### Decrypt with a TLS key log file

Use browser-style SSLKEYLOGFILE material for TLS 1.2/1.3 sessions.

```
ssldump -l sslkeys.log -r capture.pcap
```

Outcome: sessions whose secrets appear in the key log are decrypted regardless of forward secrecy, showing the plaintext payload inside the record dump.

*No corpus evidence — derived from upstream documentation.*
