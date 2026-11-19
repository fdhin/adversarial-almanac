---
technology: "Android Debug Bridge daemon (adbd) — network-exposed ADB"
kind: overview
audience: offensive-security
family: network-services
layer: "on-device debug daemon: classic TCP/5555 plaintext listener (shell by design) vs Android 11+ Wireless debugging (TLS, pairing, dynamic mDNS-advertised ports)"
language: "C/C++ (AOSP)"
homepage: "https://developer.android.com/studio/command-line/adb"
standards: "ADB wire protocol (CNXN/AUTH/OPEN packets); Android 11+ wireless debugging over TLS with mDNS advertisement"
evidence: "zero corpus evidence by design — upstream-only new unit (bucket-2, owner-approved)"
status: upstream-only
source: "rewritten from HackTricks (src/network-services-pentesting/5555-android-debug-bridge.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only: zero corpus evidence by design (new-unit bucket-2; built from the upstream reference plus primary sources, not the writeup corpus). Upstream-verified 2026-11-13: AOSP 'Architecture of ADB Wifi' (docs/dev/adb_wifi.md, android-vts-15.0_r2) — legacy flow USB→key accept→'adb tcpip 5555'→'adb connect IP:5555'; legacy TCP channel unencrypted ('can be eavesdropped and open to MITM attacks' per AOSP); pairing keystore /data/misc/adb/adb_keys or /adb_keys; pairing server RSA-2048 x509 bootstrapped by six-digit code or QR; Wireless-debugging adbd listens on a RANDOM port, greets with A_STLS/TLS where legacy greets with A_AUTH; mDNS service types _adb._tcp (legacy), _adb-tls-pairing._tcp, _adb-tls-connect._tcp. Android developer docs (developer.android.com adb) — adbd provides interactive shell on device. ro.adb.secure / ro.debuggable / service.adb.tcp.port / settings adb_enabled gating per AOSP property model as carried by the reference. CNXN probe packet structure (24-byte header: 'CNXN', version 0x01000001, maxdata, 'host::' identity) per the ADB wire protocol as documented in the reference's worm-propagation research (Hagaseca loader analysis, darkatlas.io). Rewritten from HackTricks (src/network-services-pentesting/5555-android-debug-bridge.md), CC BY-NC 4.0, accessed 2026-11-13."
---

# Android Debug Bridge daemon (adbd)

> A shell server that ships with the operating system. When adbd listens on TCP, every construct that stops an attacker — lock screen, app sandbox, store review — sits behind a protocol whose documented purpose is granting shell, installing packages, and reading app data.

## What it is

ADB is a triple: a client binary on your host, a local `adb` server routing traffic, and **adbd**, the daemon running on every Android device. Over USB this is a developer convenience. Over TCP it is a remote-administration backdoor with a single gate: the **authentication property**. Classic mode (`adb tcpip 5555`, or a vendor image that starts it anyway) opens a plaintext listener on **TCP/5555** — AOSP's own architecture document notes the channel can be eavesdropped and man-in-the-middled. Android 11+ replaced it with **Wireless debugging**: adbd picks a random port, advertises it over mDNS, and negotiates TLS after a pairing step (RSA keys bootstrapped by a six-digit code shown on screen). The port numbers differ, the risk model does not: past the handshake you hold a shell.

The gate to read on any target is `ro.adb.secure`: `1` (production phones) means adbd demands the RSA challenge-response — your key must already be in `/data/misc/adb/adb_keys`, or you need the one-time pairing code. `0` — engineering/`userdebug` builds, emulators, and a long tail of vendor IoT, set-top, TV, and development images — means anyone who reaches the port connects directly, typically as the `shell` uid, sometimes as root (`adb root` works where the build allows). No CVE is required here; this is the daemon working as designed, which is why exposed 5555 listeners have fueled whole worm families.

The operator's mental model: **reachability + `ro.adb.secure=0` = device ownership** — package install (`adb install -g`), arbitrary activity/broadcast launch (`am`/`pm`), per-app data theft (`run-as`, or direct `/data` reads as root), and port forwarding both ways for pivoting.

## The anatomy that matters on an engagement

    TCP/5555 (classic)  ──or──  dynamic TLS port (Android 11+, found via mDNS)
        ▼
    raw ADB protocol: CNXN connect packet ("host::" identity)
        ▼
    AUTH challenge-response (ro.adb.secure=1)  ──or──  straight CNXN accept (secure=0)
        ▼
    shell service  →  uid shell (or root on permissive builds)
        ▼
    pm install / am start / run-as <pkg> / adb forward / adb reverse   ← full device control

Structural properties that drive findings:

1. **The protocol is an oracle before any auth.** Send a `CNXN` packet; the first four bytes of the reply — `CNXN` (accepted, likely unauthenticated) or `AUTH` (key-gated) — classify the target in one round trip.
2. **Auth is binary and property-driven.** There is no rate limiting, no account lockout, and no logging worth the name on `ro.adb.secure=0` images; the realistic targets are exactly those images.
3. **The shell uid is not the boundary people hope.** `shell` can install packages with granted permissions, start activities, forward ports, and read any `debuggable` app's data via `run-as`; on `userdebug` builds `adb root` escalates past it.

## Where you'll meet it

- **Cloud Android containers** (Redroid-style farms, app-rating botnets, scrapped emulator fleets) where operators publish `0.0.0.0:5555` — the classic misconfiguration; the fix that keeps recurring in incident reports is binding to loopback behind a tunnel.
- **Smart TVs, set-top boxes, IVI head units, POS and kiosk hardware** on `userdebug`/engineering images, or where vendors enabled `service.adb.tcp.port` for support tooling.
- **Developer desks and MDM fleets** — Android 11+ Wireless debugging left on, discoverable via `_adb-tls-*` mDNS records on the office LAN; pairing code theft (shoulder-surf, screen recording, MDM screenshot) converts to a long-lived trusted channel.
- **Compromised-device propagation** — implanted malware speaking the ADB wire protocol itself, scanning /16s for 5555 and shaking hands with `CNXN`; the probe doubles as your fingerprinting tool.

## Reading the framework's mind

1. One 30-byte packet classifies the listener: a `CNXN` connect packet (header `CNXN`, version `0x01000001`, maxdata 4096, identity `host::`) — reply starting `AUTH` means key-gated, `CNXN` means open. This is also the pattern to alert on defensively: one source fanning CNXN probes across many hosts.
2. mDNS names the surface on modern devices: `_adb._tcp` (legacy plaintext), `_adb-tls-pairing._tcp` (pairing server active), `_adb-tls-connect._tcp` (TLS adbd ready). `adb mdns services` lists them from any paired position.
3. Post-shell triage is four properties: `getprop ro.adb.secure ro.debuggable service.adb.tcp.port` plus `settings get global adb_enabled adb_wifi_enabled` — they explain why the port was open and what an IR team must turn off.
4. `adb forward`/`adb reverse` turn the device into a pivot without root: forward reaches device-local services (Termux SSH, app debug servers) from your host; reverse exposes your host services to device apps as `127.0.0.1`.
5. Loot concentrates in `/data/data/<pkg>` (root or `run-as`), `/data/system/users/0/accounts.db`, and `/data/misc/wifi/` on older builds. Details in the [[adbd-bug-bounty|methodology]].

## Where to go next

- [[adbd-bug-bounty|adbd bug bounty methodology]] — connect/enumerate commands, the raw CNXN probe, post-exploitation and pivoting, wireless-debugging pairing, IR queries.
- Related units: [[mdns]] (how the modern surface is discovered), [[wifi]] (the LAN context of pairing-code attacks), [[iso-8583]] (POS terminals where an exposed adbd leaks the payment app).
