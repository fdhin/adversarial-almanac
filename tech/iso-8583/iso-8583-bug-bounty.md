---
technology: "ISO 8583 — card-originated financial transaction messaging"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
version: 1.0 (upstream-only new unit)
source: "rewritten from HackTricks (src/network-services-pentesting/pentesting-iso-8583-payment-sockets.md), CC BY-NC 4.0, accessed 2026-11-13"
status: upstream-only
provenance: "upstream-only: zero corpus evidence by design — methodology built from the upstream reference and primary sources. Verified 2026-11-13: message grammar (MTI digit semantics incl. 0100/0200/0400/0800 classes; 8-byte primary bitmap with bit-1 secondary extension; LLVAR/LLLVAR typing) and the data-element dictionary (DE2 n..19 PAN, DE4 n12 amount, DE11 n6 STAN, DE37 an12 RRN, DE39 an2 response code, DE41 ans8 terminal, DE42 ans15 merchant, DE55 ICC/EMV, DE64 b64 MAC) against the ISO 8583 standards summary (Wikipedia) and jPOS bitmap-packing notes as cited by the reference; logcat capture patterns against Android developer docs; APK-reversing, AUTH/JWT bootstrap, replay/dedup-window, cross-merchant RRN, terminal-check-bypass and parser-fuzz technique families carried from the reference's POS-app research writeup (m4kr0, 'ISO 8583 Under Fire') as reported technique, rewritten. No CVEs claimed. Companion overview: [[iso-8583|ISO 8583]]."
---

# ISO 8583 bug bounty methodology

Target model: an Android POS/payment app whose money path is a raw TLS socket, plus the processor endpoint it talks to. Precondition is usually an account on the app (merchant login) and authorized scope over the processor behaviors tested. End states: unauthenticated frame processing, duplicate-charge replays, cross-merchant void/refund, amount tampering, ghost transactions (processor state with no merchant/audit trail). Frame mutations touch real ledgers — test against sandboxes or explicitly authorized flows only.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Socket endpoint discovery | APK in hand | n/a (static) | host/port, debug toggles, bootstrap grammar — recon |
| Frame capture | device/app access | no | raw request/response hex — recon |
| Pre-auth frame acceptance | socket reachable | yes | unauthenticated ISO processing — Critical |
| Replay (same DE11/DE37) | one captured transaction | after capture | duplicate charge when dedup lapses — Critical |
| Cross-merchant RRN | merchant-B session + merchant-A RRN | authenticated | void/refund of another merchant's transaction — Critical |
| Amount/MTI mutation | one captured request | after capture | altered settlement amounts — Critical |
| Parser desync | socket reachable | yes | crashes, field shifts, approvals on malformed frames — High |

## Phase 1 — find the hidden channel (APK)

Payments absent from the proxy does not mean absent. In the decompiled app, hunt for: raw `Socket`/`SSLSocket` usage; hardcoded processor hostnames and ports; debug flags (typically a boolean like a debug-mode switch defaulting off); a REST login flow whose token feeds a separate socket auth; code that logs raw request/response hex. Those five markers together define the payment path and usually hand you the framing grammar.

## Phase 2 — capture frames without fighting TLS

Rather than MITM the socket (pinning, custom framing), let the app print the traffic for you: flip the latent debug logging on if present (rebuild, re-sign, reinstall the APK), then harvest logs while running authorized transactions.

```bash
adb logcat | grep -iE 'iso|8583|auth|socket|tls'
adb logcat "PaymentSocket:D *:S"     # or whatever tag the logging code uses
```

Capture at minimum: one full bootstrap exchange and one complete request/response pair for each message class you intend to attack (authorization, financial, void/refund).

## Phase 3 — stand up your own client

Rebuild the framing you captured; the common vendor shape is a 4-ASCII-digit length prefix around both a bootstrap frame and the raw ISO 8583 payload:

```python
import socket, ssl

def login():
    ...  # REST login returns a JWT for the merchant account

jwt = login()
sock = ssl.create_connection(("processor.example.com", 8443))
payload = b"AUTH" + f"{len(jwt):04d}".encode() + jwt.encode()
sock.sendall(f"{len(payload):04d}".encode() + payload)
assert sock.recv(1024).startswith(b"AUTHOK")

sock.sendall(captured_or_mutated_frame)     # 4-digit length + ISO 8583 bytes
print(sock.recv(4096))
```

Decision point: if `AUTHOK` never comes, replay the exact captured bootstrap byte-for-byte first — some stacks check device serial or ordering before accepting the token.

## Phase 4 — authentication and state-machine checks

- Send a valid ISO 8583 frame **before** any `AUTH`/bootstrap frame — does the processor transact on a bare socket?
- Send malformed or truncated `AUTH` frames and check whether the socket still flips to an authenticated state.
- Take a JWT from merchant A to a fresh socket under merchant B: is the session actually bound to merchant, terminal, and device, or just to whoever holds a token?
- Where unauthenticated frames get processed, compare processor-side state with the merchant dashboard — approvals invisible to the merchant (ghost transactions) are the headline finding.

## Phase 5 — replay and duplicate detection

Replay a captured financial request: immediately; after reconnecting; after the dedup/cache window should have expired. Keep **DE11** (STAN) and **DE37** (RRN) identical to the original. Weak implementations cache duplicates only briefly and later accept the same frame as a fresh charge; robust ones bind merchant, terminal, amount, card, STAN, RRN, and transaction lifecycle together. Report exactly which combination the backend actually keys on.

## Phase 6 — cross-merchant and ownership confusion

Treat **DE37** as an object reference and test it like an IDOR:

- authenticate as merchant B; submit a void/refund referencing merchant A's RRN;
- mutate **DE41** (terminal) and **DE42** (merchant) independently of the authenticated session;
- try forged and non-existent RRNs (zero-filled values).

If the backend trusts the frame's RRN/merchant fields over the session, one merchant can move another's money.

## Phase 7 — terminal-only checks and business logic

- Recover a frame the terminal refused to send (wrong card inserted, local validation failed) from the logs and send it yourself — backend approval proves the processor validates only the reference/format, not card identity.
- Mutate **DE4**: raise above the original amount, zero it, probe negative/signed encodings the implementation might accept, flip currency mid-flow; confuse the MTI (turn a void into another reversal/advice type). Watch the ISO response **and** the dashboard — divergence is the finding.

## Phase 8 — parser robustness

Mixed proprietary encodings with LLVAR/LLLVAR and binary TLVs desynchronize easily. High-yield fuzz cases: length indicator larger (and smaller) than the real field data; bitmap bit set with the field bytes omitted; field bytes present with the bitmap bit cleared; mutated TLVs inside **DE55**. Expected failure modes: parser crash, field shifts (one field read as another), approvals on malformed requests, inconsistent dashboard state.

## Tool mapping (in prose)

A decompiler (jadx-class tooling) for the APK; adb/logcat for frame capture; Python with `ssl` plus any ISO 8583 library (jPOS for JVM projects) to build the client; a mutation harness of your own for the field dictionary; Burp stays relevant only for the REST login leg. Bug classes: CWE-306/287 (unauthenticated or unbound socket sessions), CWE-294 (replay past dedup windows), CWE-639 (RRN as guessable/transferable object reference), CWE-840 (business-logic: amount/MTI mutations), CWE-20 (parser desync on length/bitmap mismatches).
