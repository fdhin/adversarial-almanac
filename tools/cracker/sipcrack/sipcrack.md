---
name: "sipcrack"
categories:
  - blackarch-cracker
version: "0.2"
homepage: "http://www.remote-exploit.org/codes_sipcrack.html"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2025-09-30"
build-system: make
license: custom
meta-checked: "2026-11-09"
github-repo: "marciosouza20/sipcrack"
github-stars: 1
github-pushed: "2018-09-01"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sipcrack

> Sniffs SIP digest authentication exchanges and cracks the captured passwords offline.

## What it's for

Martin J. Muench's SIPcrack suite attacks SIP/VoIP digest authentication
(RFC 2617 carried over SIP). It ships two halves: `sipdump` captures SIP
digest logins — live from a network interface or from an existing pcap
file — into a compact login dump, and `sipcrack` brute-forces the account
password from that dump using a wordlist or standard input, rewriting the
dump entry with the cleartext password once found. It cracks already
captured challenge/response exchanges; it does not enumerate SIP
extensions or perform registration attacks itself.

## Typical use cases

### Capture SIP logins live on the wire

Start sipdump on the interface that carries the VoIP traffic.

```
sipdump -i eth0 logins.dump
```

Outcome: sniffs SIP digest handshakes and appends each observed login to
`logins.dump`; on stop the dump holds one record per captured
authentication, ready for cracking.

### Extract logins from an existing packet capture

Feed a pcap file with `-p` instead of listening live.

```
sipdump -p capture.pcap logins.dump
```

Outcome: parses the capture file offline and writes every SIP digest
authentication found in it to `logins.dump`, in the same format a live
sniff would produce.

### Crack the captured logins with a wordlist

Run sipcrack against the dump produced by sipdump.

```
sipcrack -w passwords.txt logins.dump
```

Outcome: tests each word against the captured challenge; on success the
password is printed and the matching dump entry is updated with the
plaintext.

### Pipe generated candidates straight in

Use `-s` to read passwords from stdin, e.g. from a generator such as John
the Ripper.

```
john --incremental=alpha --stdout=8 | sipcrack -s logins.dump
```

Outcome: cracks the dump using candidates generated on the fly, without
materializing a wordlist file first.

*No corpus evidence — derived from upstream documentation.*
