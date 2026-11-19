---
name: "videosnarf"
categories:
  - blackarch-scanner
version: "0.63"
homepage: "https://ucsniff.sourceforge.net/videosnarf.html"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-09-14"
build-system: autotools
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "bit4bit/videosnarf"
github-stars: 4
github-pushed: "2015-02-04"
github-archived: true
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# videosnarf

> Offline RTP media reconstruction — feed it a pcap, get raw H.264 video and G.711/G.722/G.729/G.723/G.726 audio files out.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Turning packet captures of VoIP and video traffic into playable media without running a live capture tool. VideoSnarf reads an offline pcap, detects the RTP sessions inside (inspired by rtpbreak, and the first tool of its kind to recognize H.264-encoded RTP), and writes each stream as a raw codec file — `.h264` for video, per-codec files for the common enterprise audio codecs G.711u/A, G.722, G.729, G.723 and G.726. Use it when you captured with tshark/Wireshark/Ettercap or a switch SPAN tap and want the conversations reconstructed afterwards. Its sibling UCSniff does the live capture-and-decode variant of the same job, and videojak consumes videosnarf output to build replay attacks. Written by Sipera's VIPER Lab; the bit4bit mirror packaged by BlackArch adds G.729 decoding via bcg729.

## Typical use cases

### Extract every media stream from a capture

```
videosnarf -i capture.pcap
```

Outcome: scans the pcap for RTP sessions and writes raw media files into the working directory — `.h264` files for video streams and codec-named files for audio — listing each session as it is reconstructed (sample captures ship in the source tarball's `pcap/` directory for testing).

### Play a reconstructed video stream

```
mplayer stream.h264 -fps 30
```

Outcome: raw H.264 carries no container timing, so mplayer plays it at the frame rate you supply — 30 is typical for surveillance streams; the decoded picture is exactly what the sending camera transmitted.
