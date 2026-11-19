---
name: "sippts"
categories:
  - blackarch-voip
version: "v4.2.1.r0.g44568c8"
homepage: "https://github.com/Pepelux/sippts"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-28"
build-system: python
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Pepelux/sippts"
github-stars: 575
github-pushed: "2026-09-14"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 28
gh-activity-checked: "2026-11-09"
---

# sippts

> Pepelux's SIP pentest suite — one `sippts` command with modules for discovery, extension enumeration, digest cracking, toll-fraud checks and RTP Bleed attacks.

## What it's for

Auditing VoIP servers and devices that speak SIP. sippts (Python, GPLv3, by Pepelux) exposes its functionality as subcommands of a single `sippts` entry point: `scan` (multithreaded service discovery over UDP/TCP/TLS), `exten` (extension enumeration and auth requirement), `rcrack` (online password testing), `invite` (checking whether calls can be placed without authentication — toll fraud), `leak` (the SIP Digest Leak vulnerability discovered by Sandro Gauci), `enumerate` (allowed methods), `ping`, `flood`, `send`/`wssend` (custom messages, including over WebSockets), `sniff`/`spoof` (capture and ARP-MITM support), `dump`/`dcrack`/`pcapdump` (offline digest extraction and cracking, audio extraction from PCAPs), `astami` (Asterisk Manager Interface audit), and the `rtpbleed` family for the RTP Bleed issue. Results chain between modules (`scan -ot` feeds `exten`/`rcrack`), and output goes to plain, JSON or CSV logs. It is the active-audit counterpart to offline digest crackers such as [[sipbrute]] and to traffic generators like [[sipp]].

## Typical use cases

### Discover SIP services on a network

```
sippts scan -i 198.51.100.0/24 -r 5060-5080 -p all -ot targets.txt
```

Outcome: a multithreaded scan (default 200 threads) across UDP, TCP and TLS that lists every responding SIP device; `-ot` additionally writes the found hosts as `ip:port/proto`, the exact input format the `exten`, `rcrack` and `leak` modules accept. `-fp` fingerprints responders, `-cve` flags possible CVEs.

### Enumerate extensions on the found servers

```
sippts exten -f targets.txt -e 100-200 -oe extens.txt
```

Outcome: probes each extension in the range on every target from `targets.txt` and reports which exist and whether they demand authentication; `-oe` writes the confirmed extension list for the next step.

### Test passwords remotely

```
sippts rcrack -f targets.txt -ef extens.txt -w wordlist.txt
```

Outcome: attempts each wordlist password as the REGISTER digest response for every enumerated extension on every server and reports the valid credentials — the standard scan → exten → rcrack chain.

### Enumerate the methods a server accepts

```
sippts enumerate -i 198.51.100.10
```

Outcome: queries the server and prints the SIP methods it advertises and handles — the reconnaissance step before crafting custom requests with `send`.

### Extract and crack SIP digests offline

Turn a capture into recovered passwords without touching the server again.

```
sippts dump -f capture.pcap -o data.txt
sippts dcrack -f data.txt -w wordlist.txt
```

Outcome: `dump` pulls every SIP Digest authentication exchange (user, realm, nonce, response) out of the PCAP into SipCrack format; `dcrack` then cracks that file offline against a wordlist — or brute-forces with `-bf -min/-max` — and prints each user's recovered cleartext password.

### Check for toll fraud with unauthenticated calls

See whether the PBX places calls, or transfers them, without credentials.

```
sippts invite -i 198.51.100.10 -fu 200 -tu 555555555 -v
sippts invite -i 198.51.100.10 -tu 555555555 -t 444444444
```

Outcome: sends an INVITE for an external number without authenticating — a misconfigured context answers `100 Trying`/`200 OK` and the call proceeds (toll fraud), a hardened one rejects it with 401/404. The second form adds `-t` to transfer an answered call to a second number via REFER; `-user`/`-pass` retest with valid credentials.

### Trigger the SIP Digest Leak vulnerability

Force a phone to hand over a digest response worth cracking offline.

```
sippts leak -i 198.51.100.10
```

Outcome: runs the INVITE → BYE → forged `407 Proxy Authentication Required` dance against the target; a vulnerable device answers with a BYE carrying a digest response, which is printed to the console (`-o` saves it in SipCrack format for `dcrack`). Follow up with the offline cracking chain above.

### Detect RTP Bleed on an exposed media relay

Find RTP/RTCP ports that accept packets from anyone.

```
sippts rtpbleed -i 198.51.100.10
sippts rtcpbleed -i 198.51.100.10
```

Outcome: probes the even-port RTP range (10000-20000 by default, `-s`/`-e` to narrow) for streams that echo injected packets back — evidence that ongoing calls can be received or hijacked; `rtcpbleed` does the same for the odd-port RTCP side (default range 10001-20001).

### Flood or inject audio into a bleeding stream

Abuse the ports found by `rtpbleed`.

```
sippts rtpbleedflood -i 198.51.100.10 -r 10070
sippts rtpbleedinject -i 198.51.100.10 -r 10070 -f audio.wav
```

Outcome: `rtpbleedflood` floods the port with RTP packets (call interference/DoS), while `rtpbleedinject` first probes the stream for its sequence number, timestamp and SSRC, then replays the WAV file's audio into the ongoing call — audible injection at the far end. `-r` selects the target port and `-p` the codec payload type (default 0, PCMU).

### Craft an arbitrary SIP message

Send a hand-built request and watch the response.

```
sippts send -i 198.51.100.10 -m INVITE -ua Grandstream -fu 200 -fn Bob -fd 198.51.100.20 -tu 201 -tn Alice -td 198.51.100.30 -header "Allow-Events: presence" -sdp
sippts wssend -i 198.51.100.10 -r 443 -path /ws
```

Outcome: `send` transmits the custom message with full control of method, From/To headers, User-Agent, custom headers and SDP, printing the server's replies; `wssend` sends the same kind of crafted message over a WebSocket (`-r` port, `-path` endpoint) instead of UDP/TCP/TLS.

*Command patterns sourced from HackTricks pages (CC BY-NC reference; rewritten; stale upstream wiki forms corrected); module flags verified against the current source.*
