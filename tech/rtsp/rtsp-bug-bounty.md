---
technology: "Real Time Streaming Protocol (RTSP)"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
version: 1.0 (upstream-only new unit)
source: "rewritten from HackTricks (src/network-services-pentesting/554-8554-pentesting-rtsp.md; src/network-services-pentesting/32100-udp-pentesting-pppp-cs2-p2p-cameras.md), CC BY-NC 4.0, accessed 2026-11-13"
status: upstream-only
provenance: "upstream-only: zero corpus evidence by design — methodology built from the upstream reference and primary sources. Verified 2026-11-13 against: RFC 2326/7826 (methods OPTIONS/DESCRIBE/SETUP/PLAY, SDP response to DESCRIBE, 401 + WWW-Authenticate Basic/Digest); nmap.org nsedoc rtsp-url-brute (DESCRIBE per dictionary URL, args urlfile/threads, brute+intrusive categories); FFmpeg ffplay -rtsp_transport tcp; palant.info LookCam analysis for all PPPP/CS2 specifics (UDP/32100 MSG_HELLO plaintext F1 00 00 00; PREFIX-######-CCCCC IDs with BHCC/FHBB/GHBB prefixes; 22-letter verifier ≈5.15M combos; no server rate limiting per Marrapese DEF CON 28; P2P_Proprietary_Encrypt 4-byte effective key; PPPP_CRCEnc repeating-XOR keystream; unenforced LoginDev/pwd; DownloadFile arbitrary read; searchWiFiList → /tmp/wifi_scan.txt; 256-byte cmd overflow without canary/NX/ASLR; api.l040z.com cleartext cloud, device-ID-only auth); pmarrapese/iot lansearch for LAN discovery; Cameradar and rtsp_authgrinder named from their upstream repos. Companion overview: [[rtsp|RTSP]]."
---

# RTSP bug bounty methodology

Target model: an RTSP endpoint on TCP/554 or 8554 (camera, NVR, encoder). End states, in order of likelihood: unauthenticated stream access (200 on DESCRIBE), stream access with recovered credentials (401 → Basic/Digest attack), and — on the same estate — PPPP (CS2) devices reachable from anywhere by device ID with no real authentication. Credential and path testing stays within the authorized scope; cameras record people.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| OPTIONS method probe | 554/8554 reachable | yes | method map, server fingerprint — recon |
| DESCRIBE path brute | 554/8554 reachable | yes | valid stream URLs; 200 = open stream — Critical on surveillance |
| Basic auth credential test | 401 with `WWW-Authenticate: Basic` | yes (no lockout by design) | stream access — High/Critical |
| Digest auth credential test | 401 with Digest | online-only | stream access — High |
| PPPP rendezvous (UDP/32100) | device ID or LAN position | yes | session without any login → file read, geolocation, RCE — Critical |

## Phase 1 — fingerprint and method probe

Raw handshake first — it reveals auth posture in one packet and does not need any tool:

```bash
# Method map: reply carries "Public: ..." listing supported methods
printf 'OPTIONS rtsp://TARGET_IP:554 RTSP/1.0\r\nCSeq: 1\r\n\r\n' | nc -w 2 TARGET_IP 554

# Auth oracle: 200 = unauthenticated stream; 401 = WWW-Authenticate (note the scheme);
# 404/403 = wrong path. Double CRLF is mandatory — single-terminated requests often hang.
printf 'DESCRIBE rtsp://TARGET_IP:554 RTSP/1.0\r\nCSeq: 2\r\n\r\n' | nc -w 2 TARGET_IP 554
```

```bash
# Version + banner sweep; rtsp-url-brute is the script that matters in the rtsp-* family
nmap -sV --script "rtsp-*" -p 554,8554 TARGET_IP
```

Decision points: record the `Server:` banner (vendor fingerprint → default-credential list), the auth scheme (`Basic` vs `Digest`), and whether `DESCRIBE` and `SETUP` enforce the same gate. A camera gating `DESCRIBE` but not `PLAY` (or vice versa) is a finding by itself.

## Phase 2 — stream path discovery

```bash
nmap -p 554 --script rtsp-url-brute TARGET_IP
# tune: --script-args rtsp-url-brute.urlfile=/path/rtsp-urls.txt,rtsp-url-brute.threads=8
```

Output shape: a `discovered:` section (paths answering 200) and an `other responses:` section where `401:` entries are valid paths behind credentials — those become the credential targets in phase 3. Feed vendor-specific dictionaries (channel paths like `/ch01/0`, `/live/ch00`) via `rtsp-url-brute.urlfile`. Cameradar bundles its own route dictionary and runs the whole discovery-plus-credentials loop against a network range.

## Phase 3 — authentication

Basic is a single self-computed header, so a five-line socket client validates candidates:

```python
import base64, socket
token = base64.b64encode(b"admin:1234").decode()
req = f"DESCRIBE rtsp://TARGET_IP:554 RTSP/1.0\r\nCSeq: 2\r\nAuthorization: Basic {token}\r\n\r\n"
s = socket.create_connection(("TARGET_IP", 554), timeout=3)
s.sendall(req.encode())
print(s.recv(1024))  # 200 = credential accepted; 401 = rejected; keep the response off the report screenshots
```

- Vendor default pairs first (admin/admin, admin/1234, root/blank and the vendor's own list), then dictionary attacks with rtsp_authgrinder (Basic and Digest) or Cameradar (path + credential dictionaries in one run, then GStreamer-based stream validation and thumbnails).
- Digest: do not hand-roll — the exchange binds nonce, realm, and URI; a wrong URI in the A2 hash rejects valid passwords.
- No protocol-level lockout exists; bound your own rate and stop at the first hit.

## Phase 4 — view and confirm impact

```bash
# TCP-interleaved RTP: avoids UDP delivery failures through firewalls/NAT
ffplay -rtsp_transport tcp rtsp://TARGET_IP:554/live.sdp
# authenticated: ffplay -rtsp_transport tcp "rtsp://admin:PASSWORD@TARGET_IP:554/live.sdp"
```

A rendered frame is the impact statement; a 200 without decodable media is a partial finding (path valid, stream dead or codec-gated).

## Phase 5 — PPPP (CS2) P2P cameras

When the camera exposes no RTSP to the internet, look for the P2P stack instead: UDP/32100 listeners on the estate, and PPPP-speaking mobile apps.

**LAN discovery** — devices answer an unencrypted search on local broadcast; use the reference lansearch script from the PPPP research tooling (pmarrapese/iot, `p2p/lansearch`).

**Fingerprint the rendezvous** — a `MSG_HELLO` to UDP/32100 starts with the fixed plaintext `F1 00 00 00`; a reply marks a PPPP server or device:

```bash
printf '\xf1\x00\x00\x00' | nc -u -w 2 TARGET_IP 32100 | xxd
```

**Get the infrastructure map** — decompile the Android/iOS/Windows client; the "init string" holds the obfuscated rendezvous/relay server IP lists and protocol keys (some apps embed dozens of init strings, one per OEM network). Extracting it converts a device ID into a reachable session: query the device's three rendezvous servers, UDP-punch or fall back to relay.

**Work the ID space** — format `PREFIX-######-CCCCC` (prefixes seen in the LookCam family: BHCC, FHBB, GHBB). Harvest beats guessing: IDs appear in app screenshots, support tickets, and as the onboarding AP's SSID. Otherwise guess `PREFIX` + serial and attack the five-letter verifier (22-letter alphabet, ≈5.15M combos, no server-side rate limiting reported) — distributed guessing is the documented threat model gap.

**Exploit the unauthenticated control plane** (session established, authorized target):

1. Skip `{"cmd":"LoginDev",...}` entirely — firmware accepts operational commands without it, and per-command `pwd` fields go unchecked.
2. `{"cmd":"searchWiFiList"}` then `DownloadFile` on `/tmp/wifi_scan.txt` — the `iwlist` output (BSSIDs) survives on disk; submit the BSSIDs to an authorized geolocation service to demonstrate physical-location disclosure.
3. `DownloadFile` with arbitrary paths is a full file-read primitive — pull firmware/config artifacts through it.
4. Memory safety (lab only): an over-length JSON `cmd` (>255 bytes) overflows a 256-byte stack buffer in typical handlers; the analyzed builds ship without canary, NX, and ASLR, leaving a single-stage shellcode or ROP path on the device's ARM CPU. Verify per target — build settings vary across OEMs.
5. Cloud: the vendor endpoints are plain HTTP (recordings in hardcoded 5 MiB chunks), authenticated by device ID alone — knowing the ID is full access to stored footage, and a third party can enable collection for an ID that is not theirs. Report the ID-only authorization, not the endpoint traffic.

## Tool mapping (in prose)

nmap NSE (`rtsp-url-brute` and the `rtsp-*` family) for discovery; Cameradar for the full discover→attack→thumbnail loop; rtsp_authgrinder for bounded Basic/Digest credential testing; ffmpeg/ffplay for stream validation; netcat/xxd and a socket library for raw protocol work; the pmarrapese/iot lansearch script and any decompiler (for init strings) on the PPPP side. Bug classes: CWE-798 (default/vendor credentials), CWE-287/ CWE-306 (missing or unenforced authentication), CWE-120/121 (the PPPP cmd overflow), CWE-319 (cleartext cloud and control traffic), CWE-639 (device ID as the only capability).
