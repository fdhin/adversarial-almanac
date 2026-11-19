---
name: "videojak"
gentoo-category: "net-analyzer"
version: "2.0"
homepage: "http://videojak.sourceforge.net/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: GPL-2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# videojak

> IP video attack console from Sipera's VIPER Lab — MITM a voice/video VLAN, find the live RTP session, then replay, replace, or degrade the picture.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Demonstrating that unencrypted IP video is interceptable. VideoJak ARP-poisons its way into the media path (with CDP-spoofing modes that learn the voice VLAN ID and hop into it), watches for an active video session, and learns the RTP port plus the sequence numbers and timestamps in use. From its console you then attack the selected session: replay a previously captured session at the target, play a chosen AVI file against it, or wreck the audio and video quality as a media denial of service. It was built for the data/voice VLAN separation that UC deployments use, and analyzes H.264-class video streams. For replay attacks it pairs with its sibling tools: extract the stream from a capture with videosnarf, convert to AVI with ffmpeg, then feed it back through the console. Console keys: `f` detects video streams, `s` (or Ctrl+C) stops an attack.

## Typical use cases

### Intercept and attack on a flat network

```
videojak -i eth0
```

Outcome: poisons the segment on eth0, detects ongoing IP video sessions, and opens the attack console — pick a session, then replay it, inject an AVI, or degrade it; the effect shows up at the target endpoint as replaced, looped, or frozen video and audio.

### Learn and hop into the voice VLAN over CDP

```
videojak -i eth0 -c 1
```

Outcome: CDP-spoofing learning mode discovers the voice VLAN ID (VVID); once learned, the follow-up run (`-c 0`, or `-v <VVID>` when the ID is already known) hops into that VLAN and runs the same MITM and attack workflow inside it.

### Turn a captured stream into a replay payload

```
videosnarf -i capture.pcap
ffmpeg -i capture.h264 replay.avi
```

Outcome: videosnarf pulls the raw H.264 stream out of the packet capture and ffmpeg wraps it as replay.avi; with the AVI in videojak's working directory, pressing `f` in its console finds the target video device (Cisco 7985 phone, Axis camera, other) and the replay attack loops or one-shots that file at the live session.
