---
name: "bear-c2"
categories:
  - backdoor
version: "git (2026-11-02)"
homepage: "https://github.com/s3n4t0r-0x0/bear-c2"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "s3n4t0r-0x0/bear-c2"
github-stars: 729
github-pushed: "2026-11-02"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 138
gh-activity-checked: "2026-11-09"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# bear-c2

> Adversary-emulation C2 framework whose listeners, encryption and exfiltration mimic real Russian, Chinese, North Korean and Iranian APT tradecraft.

## What it's for

BEAR-C2 is a GUI-driven command-and-control framework for red-team adversary simulation: instead of writing a bespoke C2 script for each scenario, an operator configures a listener that combines a network protocol (DoH, HTTPS, HTTP, QUIC or reverse TCP) with per-listener encryption (AES, XOR, RC4, DES, ChaCha20, RSA), optional cloud exfiltration profiles (Google Drive, OneDrive, Dropbox), DGA domains, custom JA3S fingerprints and community Malleable C2 profiles. Around that core it bundles a payload generator (EXE/DLL/BIN/HEX beacons auto-matched to a listener), Telegram- and Discord-based C2 channel agents that need no exposed server, a script obfuscator for PS1/BAT/HTML/VBS/JS/PY, a spear-phishing campaign module and a file-hosting module. It is operated through its desktop interface, not a scriptable CLI — there is no headless command API, so everything from listener creation to payload generation happens in the GUI.

## Typical use cases

### Launch the framework

Start the BEAR-C2 operator interface.

```
./BEAR-C2
```

Outcome: the BEAR-C2 desktop application opens with the Listeners Table as its hub; from there you start/stop listeners, generate matching payloads with the payload generator (its Auto Fills button clones a listener's protocol, encryption key and beacon auth ID), configure exfiltration profiles and build Telegram/Discord agents. The `BEAR-C2` binary is produced by compiling the project first — build steps live in the tool's install notes.

*No corpus evidence — derived from upstream documentation.*
