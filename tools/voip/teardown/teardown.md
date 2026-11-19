---
name: "teardown"
categories:
  - blackarch-voip
version: "1.0"
homepage: "http://www.hackingexposedvoip.com/"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-06"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# teardown

> Forges SIP BYE requests to tear down a live call, using the Call-ID and dialog tags read out of a sniffer capture.

## What it's for

Demonstrating that unauthenticated signalling can end other people's calls. teardown (v1.0, February 2006, by Mark D. Collier and Mark O'Brien of SecureLogix — the "BYE Teardown" tool from the Hacking Exposed VoIP companion site) takes the four identifiers that name a SIP dialog — Call-ID, From tag and To tag, plus the target addresses — and injects crafted BYE requests towards the endpoints, forged so each side believes the other hung up. It builds packets with libnet via the authors' hack_library, spoofs its source port from the discard port 9 by default and aims at port 5060 (`-S`/`-D` override both). You need the dialog identifiers first, which in practice means capturing the live call with Wireshark and reading the Call-ID and tags out of its INVITE/200 OK exchange; a `-l` line-string option exists for SNOM phones. It is a surgical single-purpose tool: it ends calls, it does not intercept or modify them (for audio injection see [[rtpinsertsound]] and [[rtpmixsound]]).

## Typical use cases

### Tear down an in-progress call

Seven positional parameters: `teardown <interface> <target user> <IPv4 addr of target domain> <IPv4 addr of target> <CALL ID> <From tag> <To tag>`.

```
teardown eth0 5000 198.51.100.10 198.51.100.20 70710@198.51.100.10 55a66b 1208.12
```

Outcome: the tool forges BYEs for the dialog identified by Call-ID `70710@198.51.100.10` with From tag `55a66b` and To tag `1208.12`, sourced from the interface's IP towards the two targets; with correctly captured identifiers both endpoints hang up the call. `-v` prints the packets as they are sent; a mismatch in any identifier simply produces no effect, so verify the tags against a fresh capture.

*No corpus evidence — derived from upstream documentation.*
