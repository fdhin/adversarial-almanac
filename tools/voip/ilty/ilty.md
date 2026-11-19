---
name: "ilty"
categories:
  - blackarch-voip
version: "1.0"
homepage: "http://chdir.org/~nico/ilty/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ilty

> Curses console that passively taps VoIP conversations, playing the audio live and recording each call to an Ogg Vorbis file.

## What it's for

ilty (Nicolas Bareil, 2006) is a small Python interception phone system for VoIP networks. It sniffs an Ethernet segment, tracks SIP and Cisco Skinny (SCCP) signalling, follows the RTP streams of the conversations it sees, and pipes the audio through sox — live playback via esdcat plus automatic recording of each conversation to `conversation-<id>.ogg`. It is entirely passive: no packets are injected, it only listens to traffic that reaches the host. The packaged script takes no command-line options and hardcodes `eth0` as the capture interface, so sniffing another interface means editing the script.

## Typical use cases

### Tap live conversations on the voice segment

Run it as root on a host that can see the VoIP traffic (mirror port or shared segment) and watch the call list build.

```
ilty
```

Outcome: opens a bordered curses screen listing each detected conversation and its parties; audio of tracked calls is played live through esdcat and recorded to `conversation-N.ogg` files in the working directory; type a conversation's number to select it, `view` for per-phone detail, `back` to return, and `quit` to exit.

*No corpus evidence — derived from upstream documentation.*
