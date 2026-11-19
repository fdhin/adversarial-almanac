---
name: "storm-ring"
categories:
  - blackarch-voip
version: "0.1"
homepage: "http://packetstormsecurity.com/files/115852/Storm-Ringing-PABX-Test-Tool.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# storm-ring

> Rings every phone behind a PBX at once by sending SIP INVITEs for each internal number in a dictionary — a test for misconfigured "allow guest" dialing.

## What it's for

Proving that an anonymous caller can place calls on a PBX. Storm Ring (v0.1, 2012, Python 2, by Francesco Tornieri, distributed via Packet Storm) targets PABXes configured with `allow guest = yes`: it reads a dictionary of internal telephone numbers (`dic`), fills the placeholders of a bundled SIP INVITE template (the `payload` file) using settings from `variable.cfg`, and sends one INVITE per number over UDP so that all Terminal Equipments — the phones — ring simultaneously. The tool was developed against Asterisk and expects that family of behaviour; it demonstrates the anonymous-calling weakness and nothing more — there is no exploitation beyond placing the calls. If the phones stay silent, guest dialing is closed; if they ring, the PBX is misconfigured and open to toll fraud and harassment at scale.

## Typical use cases

### Ring all extensions on a guest-enabled PBX

```
storm-ring -d dic -t 198.51.100.10
```

Outcome: for every number listed in `dic`, a filled INVITE is sent to the PBX at 198.51.100.10; the phones ring across the site, demonstrating that anonymous callers can reach internal terminals. Edit `variable.cfg` (local user, host, agent, display name, method, CSeq) and the `payload` template to match the target before running; `-h` prints the short usage (`-d` dictionary, `-t` PABX IP).

*No corpus evidence — derived from upstream documentation.*
