---
name: "pyrdp"
categories:
  - blackarch-sniffer
version: "2107.b610809"
homepage: "https://github.com/GoSecure/pyrdp"
packaged-by: [blackarch]
blackarch-builddate: "2026-06-01"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "GoSecure/pyrdp"
github-stars: 1787
github-pushed: "2026-05-13"
github-archived: false
github-issues: 62
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 13
gh-activity-checked: "2026-11-09"
---

# pyrdp

> Python 3 RDP monster-in-the-middle: intercept an RDP session, harvest credentials and files, record and replay it.

## What it's for

PyRDP (GoSecure) sits between an RDP client and its server — you must first get the traffic to it (ARP spoofing, DNS redirection, a rogue host, or a transparent gateway position; it does not redirect traffic itself). It terminates the client's TLS with a certificate of its own and proxies to the real server while it: logs plaintext credentials or NetNTLM hashes from the handshake, captures clipboard contents and transferred files, crawls mapped drives in the background, records whole sessions to `.pyrdp` replay files, and can auto-run console or PowerShell payloads on new connections. Four front-ends ship with it: `pyrdp-mitm` (the MITM), `pyrdp-player` (watch live sessions, view replays, covertly take control), `pyrdp-clonecert` (forge a certificate matching a real server's so the MITM's looks legitimate), and `pyrdp-convert` (replays and pcaps to video, JSON events or replays). Servers enforcing NLA need either the server's real certificate and key (`--auth ssp`) or its redirection feature.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Intercept RDP sessions

Position clients at the MITM and capture everything.

```
sudo pyrdp-mitm 198.51.100.20:3389
```

Outcome: pyrdp-mitm listens for RDP clients and proxies them to the target; on the first connection a `pyrdp_output/` tree appears with `certs/`, `files/` (captured transfers, deduplicated by SHA-256), `filesystems/` (recreated crawled shares), `logs/` (mitm.log with credentials, ntlmssp.log with NetNTLM hashes, ssl.log with Wireshark-compatible master secrets) and `replays/` (.pyrdp recordings).

### Present a certificate that looks like the server's

Reduce client suspicion with a cloned certificate.

```
pyrdp-clonecert 198.51.100.20 cert.pem -o key.pem
sudo pyrdp-mitm 198.51.100.20 -c cert.pem -k key.pem
```

Outcome: pyrdp-clonecert connects to the server, downloads its certificate and mints a self-signed replacement with the same fields plus a freshly generated key pair, saved as cert.pem/key.pem; the MITM then presents that pair (`-c`/`-k`) to clients instead of its generated default.

### Watch sessions live and take control

Stream intercepted sessions to the player.

```
pyrdp-mitm 198.51.100.20 -i 127.0.0.1 -d 3000
pyrdp-player
```

Outcome: the MITM forwards live sessions to a player listening on 127.0.0.1:3000 (`-i`/`-d`); run `pyrdp-player` (default port 3000, `-p` to change, `-b` to bind elsewhere) in a second terminal to watch keystrokes and mouse movement as they happen and, when useful, take control of the session while hiding your actions from the client.

### Turn replays into shareable evidence

Convert recordings to video or structured events.

```
pyrdp-convert -f mp4 -o ./out pyrdp_output/replays/session.pyrdp
```

Outcome: the replay is rendered to an MP4 under `./out` (needs ffmpeg/libavcodec); `-f json` yields a sequence of low-level events instead, and `pyrdp-convert --list-only capture.pcap` enumerates the RDP sessions — and which are decryptable — before converting a raw capture.
