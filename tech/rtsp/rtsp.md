---
technology: "Real Time Streaming Protocol (RTSP)"
kind: overview
audience: offensive-security
family: network-services
layer: "media session control (TCP/554, 8554) over RTP/RTCP delivery — plus the PPPP (CS2) UDP/32100 P2P camera stack vendors ship when NAT blocks direct RTSP"
standards: "RFC 2326 (RTSP 1.0), RFC 7826 (RTSP 2.0)"
evidence: "zero corpus evidence by design — upstream-only new unit (bucket-2, owner-approved)"
status: upstream-only
source: "rewritten from HackTricks (src/network-services-pentesting/554-8554-pentesting-rtsp.md; src/network-services-pentesting/32100-udp-pentesting-pppp-cs2-p2p-cameras.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only: zero corpus evidence by design (new-unit bucket-2; built from the upstream reference plus primary sources, not the writeup corpus). Upstream-verified 2026-11-13: RFC 2326/7826 method semantics (OPTIONS/DESCRIBE/SETUP/PLAY; DESCRIBE returns SDP; 401 carries WWW-Authenticate Basic or Digest; RTSP 2.0 in RFC 7826 obsoletes RFC 2326, still widely unimplemented by devices); nmap NSE rtsp-url-brute verified against nmap.org nsedoc (portrule, categories brute+intrusive, sends DESCRIBE per dictionary URL, args rtsp-url-brute.urlfile / rtsp-url-brute.threads, output splits 'discovered' from 401 responses); ffplay -rtsp_transport tcp (force TCP-interleaved RTP) per FFmpeg docs; PPPP/CS2 specifics verified against the primary LookCam analysis (palant.info, 2025-09-08): rendezvous over UDP/32100 with plaintext MSG_HELLO F1 00 00 00, ID format PREFIX-######-CCCCC (BHCC/FHBB/GHBB prefixes, ~1M devices per prefix, 22-letter verifier alphabet minus A/I/O/Q ≈ 5.15M combos), optional P2P_Proprietary_Encrypt with 4-byte effective key, PPPP_CRCEnc = fixed repeating XOR keystream, unenforced LoginDev/password checks, DownloadFile arbitrary read, searchWiFiList → /tmp/wifi_scan.txt geolocation chain, 256-byte cmd stack overflow with no canary/NX/ASLR, cleartext HTTP cloud at api.l040z.com authenticated by device ID alone. Rewritten from HackTricks (src/network-services-pentesting/554-8554-pentesting-rtsp.md; src/network-services-pentesting/32100-udp-pentesting-pppp-cs2-p2p-cameras.md), CC BY-NC 4.0, accessed 2026-11-13."
---

# Real Time Streaming Protocol (RTSP)

> The VCR-remote of IP video: HTTP-shaped requests that hand you a live camera feed if you guess the path and one weak credential. And when the camera hides behind NAT, its vendor ships a second, far worse protocol to reach it anyway.

## What it is

RTSP is a text control protocol for streaming media sessions, standardized as RFC 2326 (1.0) and re-issued as RFC 7826 (2.0) — which most cameras and encoders in the field still do not implement. It looks like HTTP on the wire (request line, headers, status codes) but controls a session instead of fetching a document: `OPTIONS` advertises methods, `DESCRIBE` returns an SDP description of the media, `SETUP` negotiates the transport (client/server RTP ports), and `PLAY` starts delivery. RTSP never carries the media itself — that is RTP with RTCP, or a vendor-proprietary transport.

The operator takeaway: every one of those verbs is an oracle. A bare `DESCRIBE rtsp://TARGET_IP:554 RTSP/1.0` answers `200 OK` on an unauthenticated camera or `401 Unauthorized` with a `WWW-Authenticate` header naming the scheme (`Basic` or `Digest`) on an authenticated one — one packet tells you whether the stream is open, and if not, exactly what stands between you and it. Default ports are 554 and 8554; on surveillance estates the realistic findings are default vendor credentials, dictionary-word credentials, and unauthenticated legacy firmware — the protocol has no lockout, rate limit, or account-lockout concept of its own.

`Basic` auth is Base64(username:password) in the `Authorization` header — trivially decoded, brute-forceable offline-valid, and pointless without TLS (RTSP-over-TLS, RTSPO, is rare on cameras). `Digest` keeps the password off the wire but demands correct nonce/realm/URI handling from the client; prefer attacking it with a tool that implements the exchange rather than hand-rolled requests.

## The anatomy that matters on an engagement

    TCP/554 or 8554
        ▼
    OPTIONS rtsp://TARGET_IP:554  →  "Public: OPTIONS, DESCRIBE, SETUP, ..."   ← method map, free
        ▼
    DESCRIBE rtsp://TARGET_IP/live.sdp  →  200 + SDP (open stream)
                                     └─→ 401 + WWW-Authenticate (scheme reveal)
        ▼
    SETUP (transport: client_port=RTP/RTCP pair)  →  session id
        ▼
    PLAY  →  RTP/RTCP (or proprietary) media flow → ffplay and done

Structural properties that drive findings:

1. **The path is the password.** Most cameras expose streams at guessable URLs (`/live.sdp`, `/mpeg4`, `/h264`, channel-specific variants); a dictionary of paths against `DESCRIBE` separates 200s (open) from 401s (valid path, credentialed) from 404s (dead end).
2. **The auth surface is exactly one scheme family.** Whatever `WWW-Authenticate` names is what you attack — Basic means offline-able credential testing, Digest means online-only with proper tooling.
3. **Session state is minimal and permissive.** Cameras rarely bind a `PLAY` to proof of `DESCRIBE` ownership; on older firmware, path + any valid credential equals video.

## Where you'll meet it

- **IP cameras and NVRs** — the dominant exposure; vendor default credential lists and `/live.sdp`-style paths are the canonical finding. Internet-wide search engines index hundreds of thousands of open RTSP endpoints.
- **Broadcast and encoder gear** (Teradek-style contribution encoders, matrix switchers) where `DESCRIBE` is often unauthenticated by design.
- **Smart-home and IoT firmware** — the same SoCs that speak RTSP on the LAN frequently also speak the P2P stack below, which punches outward through NAT and is reachable from anywhere by design.
- **Enterprise videoconferencing room systems** exposing auxiliary RTSP feeds on management ports.

## Reading the framework's mind

1. One raw request fingerprints the auth posture: `DESCRIBE rtsp://TARGET_IP:554 RTSP/1.0\r\nCSeq: 2\r\n\r\n` — the trailing blank line (double CRLF) is required or many servers never answer. `200` = open stream, `401` = credentialed, `404`/`403` = wrong path or blocked.
2. `OPTIONS` costs nothing and returns a `Public:` header listing implemented methods — devices that skip `DESCRIBE` auth but gate `PLAY` (or vice versa) show up here first.
3. Path dictionaries are the highest-yield first move: the nmap `rtsp-url-brute` script fires a `DESCRIBE` per dictionary URL and reports which paths answered 200 vs 401. Swap dictionaries with `rtsp-url-brute.urlfile`.
4. Basic auth is a single `Authorization: Basic <base64>` header — a five-line socket script validates candidate credentials; dedicated tools (Cameradar, rtsp_authgrinder) wrap path + credential dictionaries and thumbnail the results.
5. Once a path and credential check out, `ffplay -rtsp_transport tcp rtsp://TARGET_IP/live.sdp` confirms the stream end-to-end (TCP-interleaved RTP avoids the UDP-through-firewall failure that makes open streams look dead).

## The sibling surface: PPPP (CS2) P2P cameras

Low-cost camera families that must be reachable from outside without port forwarding do not expose RTSP to the internet at all — they ship the proprietary **PPPP** stack (CS2 Network, licensed as a black-box library by hundreds of OEMs, tens of millions of devices). PPPP is an ID-addressed, TCP-like layer over UDP: devices register with three vendor rendezvous servers per ID prefix (listener commonly on **UDP/32100**), clients query the same servers for the device's public mapping, then both sides attempt UDP hole punching with relay servers as fallback.

The attack model is entirely different from RTSP — no ports, no paths, just a **device ID**:

- **ID format** `PREFIX-######-CCCCC` (e.g. `GHBB-000001-NRLXW`, shortened to `G000001NRLXW` in apps). Known prefixes: `BHCC`, `FHBB`, `GHBB`. The five-letter verifier draws from 22 uppercase letters (no A/I/O/Q) — about 5.15M combinations per serial number — and the servers reportedly implement no rate limiting. IDs leak constantly: they appear in every app screen (user screenshots), and the device's onboarding AP broadcasts the ID as its SSID.
- **"Encryption" is obfuscation.** The optional blanket cipher (`P2P_Proprietary_Encrypt`) reduces its key to an effective 4 bytes, is usually disabled outright, and falls to known-plaintext — the first four bytes of `MSG_HELLO` to UDP/32100 are always `F1 00 00 00`. The registration "cipher" (`PPPP_CRCEnc`) is a fixed repeating XOR keystream with a 4-byte padding check, so one known plaintext recovers the keystream for everything else. Server IP lists and keys live in "init strings" embedded in the mobile app, trivially extractable.
- **Authentication is theater.** The control plane is JSON commands over the PPPP stream; analyzed firmware accepts operational commands without any login (`{"cmd":"LoginDev","pwd":"123456"}` is advisory — the `pwd` field on later commands is never checked either).
- **File read and geolocation primitives.** `DownloadFile` reads arbitrary paths; `searchWiFiList` shells out to `iwlist` and leaves the full scan (BSSIDs included) in `/tmp/wifi_scan.txt` — download that, feed the BSSIDs to a geolocation service, and the camera's physical location resolves to within tens of meters.
- **Memory safety to RCE.** Command handlers copy the JSON `cmd` string into fixed 256-byte stack buffers with `memcpy` and no bound check; builds lack canaries, NX, and ASLR, so an over-length command is a direct stack overflow on the device's ARM CPU.
- **Cloud with no auth.** Uploads go to the vendor cloud over cleartext HTTP (5 MiB chunks, authenticated by device ID alone); boot-time signurl requests mean the server — or anyone who knows the ID and buys a cloud package — can switch recording on regardless of what the app displays.

## Where to go next

- [[rtsp-bug-bounty|RTSP bug bounty methodology]] — the raw DESCRIBE/OPTIONS probes, path and credential attacks, viewing, and the PPPP session workflow.
- Related units: [[sip]] (the other SDP/RTP session-control protocol, with its own auth failures), [[upnp]] and [[wifi]] (the LAN/IoT estates where these cameras live), [[ipmi]] for the server-side cousins of out-of-band camera access.
