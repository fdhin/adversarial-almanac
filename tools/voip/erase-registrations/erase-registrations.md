---
name: "erase-registrations"
categories:
  - blackarch-voip
version: "1.0"
homepage: "http://www.hackingexposedvoip.com/"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: make
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# erase-registrations

> Sends a wildcard SIP REGISTER with Expires: 0 that erases every registration a user holds on a registrar.

## What it's for

Part of the Hacking Exposed VoIP toolset (Mark D. Collier and Mark O'Brien, SecureLogix, 2006). It transmits a single SIP REGISTER request carrying `Contact: *` and `Expires: 0` — the RFC 3261 wildcard deregistration — which removes all current bindings for the named user instead of adding one. It is a targeted availability test: a user whose registrations are erased cannot receive calls until their phone re-registers. Despite the distro package description, it is a SIP tool, not an IAX flooder; it sends one crafted request per run and takes no scan or flood options.

## Typical use cases

### Erase all registrations for one user

Deregister user 5000's phone by removing every binding it holds.

```
erase-registrations eth0 5000 198.51.100.10 198.51.100.20
```

Outcome: sends one crafted SIP REGISTER (`Contact: *`, `Expires: 0`, `CSeq: 100 REGISTER`) for user 5000, with 198.51.100.10 as the domain in the Request-URI, to the registrar at 198.51.100.20; the tool prints its version banner and exits, and the user's registrations disappear until the phone re-registers.

### Wipe registrations for every user on a registrar

Pass an empty user to target all users of the domain at once, verbosely.

```
erase-registrations -v eth0 "" 198.51.100.10 198.51.100.20
```

Outcome: transmits the wildcard deregistration with an empty user part; on a registrar that honours wildcard REGISTERs, every binding at the domain is dropped, and `-v` prints details of the packet crafting as it happens.

*No corpus evidence — derived from upstream documentation.*
