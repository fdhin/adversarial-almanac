---
name: "redirectpoison"
categories:
  - blackarch-voip
version: "1.1"
homepage: "http://www.hackingexposedvoip.com/"
packaged-by: [blackarch]
blackarch-builddate: "2020-02-01"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# redirectpoison

> Sniffs a phone's outgoing SIP INVITEs and answers each with a forged 301 redirect carrying an attacker-chosen Contact.

## What it's for

Another Hacking Exposed VoIP toolset utility by Mark D. Collier and Mark O'Brien (SecureLogix, 2006), sharing its hack_library with erase-registrations. It watches SIP traffic from one target IPv4 address and port, and whenever the target issues an INVITE it injects a spoofed 301 Moved Permanently response whose Contact header is a URI of your choosing — the phone then displays "Forwarding to \<name\>" and redirects the call there. It presumes only that the target's SIP signalling reaches the sniffing interface in promiscuous mode (hub or mirror port); it is not itself a full man-in-the-middle. It must run as root and raises its own priority to -20 so the poison beats the real server's response.

## Typical use cases

### Redirect a phone's calls to another URI

Answer the target phone's INVITEs with a 301 pointing at a URI you control.

```
redirectpoison eth0 198.51.100.10 5060 "voicemail <sip:4500@198.51.100.20;transport=udp>"
```

Outcome: prints the banner, target `198.51.100.10:5060` and the poison, then listens; each INVITE from the target triggers a 301 whose Contact is the poison (the phone shows "Forwarding to voicemail"), and on Ctrl-C it prints metrics — packets sniffed, INVITEs seen and poisoned replies sent.

### Poison every INVITE without pre-poisoning checks

Omit the user part from the URI to disable the duplicate-poisoning detection.

```
redirectpoison -v eth0 198.51.100.10 5060 "<sip:198.51.100.20;line=xtrfgy>"
```

Outcome: warns that pre-poisoning assessment is disabled and reacts to every INVITE from the target — including re-INVITEs, which commonly tears the affected call down — with `-v` printing details of each sniffed and injected packet.

*No corpus evidence — derived from upstream documentation.*
