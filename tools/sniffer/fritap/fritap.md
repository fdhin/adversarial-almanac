---
name: "fritap"
categories:
  - sniffer
homepage: "https://github.com/fkie-cad/friTap"
origin: "awesome-pentest"
recipe: "upstream"
packaged-by: []
build-system: python
github-repo: "fkie-cad/friTap"
github-stars: 569
github-match: "exact"
gh-checked: "2026-11-14"
github-commits-year: 144
gh-activity-checked: "2026-11-14"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-14"
license: see repository
---

# fritap

> Frida-based real-time SSL/TLS key extraction and traffic decryption — attach to a process by name or PID and get plaintext PCAPs or a Wireshark-ready keylog.

## What it's for

friTap (FKIE) automates the analysis of traffic wrapped in SSL/TLS by instrumenting the target's crypto libraries with Frida (inspired by Google's ssl_logger): it hooks OpenSSL, BoringSSL, NSS, GnuTLS, WolfSSL, MbedTLS, Conscrypt, S2n-tls, RusTLS and friends to dump TLS secrets in real time and/or reassemble the plaintext traffic into a PCAP. Where the vault's [[ecapture]] does the same job with eBPF probes on Linux, friTap rides Frida — so it also covers Windows and Android/iOS, reaches statically-linked or symbol-stripped BoringSSL (Flutter, Cronet) via byte patterns, and can recover secrets straight from heap memory when hooking is not an option. Typical drivers are malware traffic analysis and app privacy auditing. Platform capability varies per library (full plaintext+keys, keys only, or read/write hooks only — check the upstream support matrix); running `fritap` with no arguments opens a guided TUI wizard.

## Typical use cases

### Decrypt a desktop process's TLS into a PCAP

```
sudo fritap --pcap mycapture.pcap thunderbird
```

Outcome: friTap attaches to the named process (or PID), hooks its SSL library, and writes the decrypted application traffic to `mycapture.pcap` as it happens — no proxy, no planted CA.

### Extract only the TLS keys

```
sudo fritap -k key.log target
```

Outcome: TLS secrets are appended to `key.log` in keylog format; capture packets separately and Wireshark decrypts the session from the log, which stays correct even where a library supports key extraction but not plaintext capture.

### Analyze a mobile app

```
fritap -m -k keys.log com.example.app
```

Outcome: with `frida-server` running on the Android/iOS device, `-m` makes friTap attach to (or spawn) the app by package name and extract its keys — plaintext pcap works the same way with `-p`, and `adb` must be in PATH.

### Recover secrets from heap memory

```
sudo fritap -ms target
```

Outcome: instead of hooking the TLS library, friTap scans the process heap for keying material — the fallback for renamed/obfuscated libraries; `-ms mtproto` (or `-ms telegram`) recovers Telegram MTProto cloud and secret-chat keys the same way.

### Hook statically-linked BoringSSL (Flutter, Cronet)

```
fritap --patterns libflutter_patterns.json -k keys.log target_app
```

Outcome: the JSON byte-pattern file (generated for `libflutter.so`-style stripped binaries by the companion BoringSecretHunter Ghidra container) tells friTap where the key functions live; keys land in `key.log`. `--offsets offsets.json` does the same from raw offsets, and `--library-scan` discovers renamed or statically-linked libraries first.

*No corpus evidence — derived from upstream documentation.*
