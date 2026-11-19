---
name: "vomit"
gentoo-category: "net-analyzer"
version: "0.2c"
homepage: "http://vomit.xtdnet.nl/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo, aur]
build-system: autotools
license: BSD
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# vomit

> Voice Over Misconfigured Internet Telephones — renders a pcap of a Cisco IP phone call into a WAV you can play anywhere.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Proof that unencrypted VoIP is wiretappable. Cisco IP phones of the era spoke unencrypted G.711 RTP; vomit ("voice over misconfigured internet telephones", by Niels Provos, 2001) reads a tcpdump/pcap of such a conversation and writes the audio as a wave file — or pipes it straight into a player. Capture the traffic yourself (tcpdump filtering the phone's host and UDP), then let vomit do the decoding. Two hard limits: it works only for G.711, and it takes a capture file rather than a live interface. The original homepage (vomit.xtdnet.nl) is still online, and Pentoo still packages the tool. Note that the same package name in AUR belongs to an unrelated Rust mail toolkit by a different author.

## Typical use cases

### Capture a call and render it to WAV

```
tcpdump -w call.pcap host 198.51.100.10 and udp
vomit -r call.pcap > call.wav
```

Outcome: the capture isolates the phone's RTP traffic, and vomit decodes the G.711 conversation from it; call.wav is an ordinary 8 kHz wave file playable in any audio player.

### Play the conversation immediately

```
vomit -r call.pcap | waveplay -S8000 -B16 -C1
```

Outcome: the upstream's canonical invocation — decoded audio (8000 samples/s, 16-bit, mono) streams straight into the player with no intermediate file on disk.
