---
name: "eapmd5pass"
categories:
  - blackarch-cracker
version: "3.3d5551f"
homepage: "http://www.willhackforsushi.com/?page_id=67"
packaged-by: [blackarch, kali]
blackarch-builddate: "2023-02-28"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "joswr1ght/eapmd5pass"
github-stars: 29
github-pushed: "2017-08-04"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# eapmd5pass

> Offline dictionary attack against EAP-MD5 (802.1X MD5-Challenge) — works from a packet capture, a live monitor-mode interface, or raw challenge/response values.

*No corpus evidence — derived from upstream documentation.*

## What it's for

EAP-MD5 authenticates a supplicant with MD5 over a shared challenge, sending nothing that protects the password's entropy — so a captured exchange can be replayed against a dictionary offline. eapmd5pass (Joshua Wright, 2007-2008; patches and sample captures by Brad Antoniewicz) is the standard tool for exactly that: it parses EAP-MD5 exchanges from a libpcap file or a live monitor-mode capture, extracts the identity, challenge and response, and hashes a wordlist at several hundred thousand guesses per second until it reproduces the response. It targets only EAP-MD5 — not EAP-TLS, PEAP or any tunneled EAP type — and EAP-MD5 itself is legacy and rarely deployed. Sample captures ship with the source (e.g. `eapmd5-sample.dump`, password `beaVIs`) for testing.

## Typical use cases

### Attack a captured exchange

Crack an EAP-MD5 handshake already saved to a capture file.

```
eapmd5pass -r capture.cap -w wordlist.txt
```

Outcome: the tool reports collecting the data needed for the observed user, then runs the dictionary — on success it prints `User password is "..."` followed by total time and guesses-per-second. If the capture holds several networks, `-b 00:11:22:33:44:55` restricts it to one BSSID (default: all).

### Capture and attack live

Sniff a monitor-mode interface instead of reading a file.

```
eapmd5pass -i mon0 -w wordlist.txt
```

Outcome: listens on `mon0` for EAP-MD5 exchanges in progress; once challenge and response for a user have been seen, it starts the dictionary attack against that exchange automatically. `-v` (up to `-vvv`) increases detail about the frames being parsed.

### Attack with only the challenge and response

No capture needed when the values were observed elsewhere (e.g. an 802.1X handshake noted by hand or dumped by another tool).

```
eapmd5pass -w wordlist.txt -U jwright -C d7:ec:2f:ff:2a:da:43:7f:9d:cd:4e:3b:0d:f4:4d:50 -R 1f:fc:6c:26:59:bc:5b:b9:41:44:fd:01:eb:75:6e:37 -E 2
```

Outcome: mounts the same dictionary attack from the manual parameters — username, 16-byte challenge, 16-byte response and the EAP ID of the response frame (usually `2`) — printing the recovered password and cracking speed. `-r` and the `-U/-C/-R/-E` set are alternatives; the usage output reminds you not to combine them.
