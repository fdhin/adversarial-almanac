---
name: "chapcrack"
categories:
  - blackarch-cracker
version: "17.ae2827f"
homepage: "https://github.com/moxie0/chapcrack"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "moxie0/chapcrack"
github-stars: 385
github-pushed: "2012-09-22"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# chapcrack

> Parse MS-CHAPv2 handshakes out of captures, reduce them to a cloud-cracking token, then decrypt the protected traffic.

## What it's for

chapcrack (Moxie Marlinspike, 2012) works with MS-CHAPv2 credentials captured from the network — PPTP VPN sessions from a packet capture, or WPA2-Enterprise inner auth caught with a rogue RADIUS endpoint such as FreeRADIUS-WPE. It parses the handshake, reduces it to the token that determines the user's NT hash, and hands you that token for an online cracking service (the original CloudCracker is gone; crack.sh has served the same MS-CHAPv2 role), which returns the NT hash. With the returned hash chapcrack can then decrypt an entire MPPE-protected PPTP capture, exposing all session traffic. It does not crack the hash locally — the design assumption is cloud GPUs — and it is a Python 2 tool packaged with a `chapcrack` wrapper by BlackArch.

## Typical use cases

### Extract the handshake from a PPTP capture

Pull the MS-CHAPv2 exchange out of a packet capture.

```
chapcrack parse -i capture.pcap
```

Outcome: prints the parsed handshake details and the chapcrack token (the two DES keys that determine the NT hash) to submit to the cracking service.

### Process a captured RADIUS challenge/response

Turn FreeRADIUS-WPE output into the same token format.

```
chapcrack radius -C <challenge> -R <response>
```

Outcome: consumes the challenge and response strings intercepted from the WPA2-Enterprise authentication and prints the chapcrack token for submission, same as the capture-parsing path.

### Decrypt a PPTP capture with the recovered NT hash

Recover the full protected session once the hash comes back.

```
chapcrack decrypt -i capture.pcap -o decrypted.pcap -n <nt-hash>
```

Outcome: rewrites the MPPE-encrypted PPTP packets into decrypted.pcap using the NT hash returned by the cracking service, producing a capture that opens normally in Wireshark.
