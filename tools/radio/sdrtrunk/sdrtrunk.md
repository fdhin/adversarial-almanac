---
name: "sdrtrunk"
categories:
  - blackarch-radio
version: "0.6.0"
homepage: "https://github.com/DSheirer/sdrtrunk"
packaged-by: [blackarch]
blackarch-builddate: "2024-11-03"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "DSheirer/sdrtrunk"
github-stars: 2223
github-pushed: "2026-11-07"
github-archived: false
github-issues: 396
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 106
gh-activity-checked: "2026-11-09"
---

# sdrtrunk

> Cross-platform Java application for decoding, monitoring, recording and streaming trunked radio systems with SDR dongles.

*No corpus evidence — derived from upstream documentation.*

## What it's for

sdrtrunk decodes trunked mobile radio protocols (P25 and related digital trunking standards, plus conventional channels) using SDR tuners — RTL-SDR, Airspy, SDRplay, HackRF and others via USB. On top of raw decoding it provides what monitoring actually needs: channel and talkgroup configuration, call events, audio recording and streaming, and a channel map of the trunked system. The release archives bundle a stripped-down Java Runtime, so no JVM install is needed; nightly builds carry the newest fixes. It is a GUI application — configuration and monitoring happen in the interface, not a CLI.

## Typical use cases

### Run the application from a release archive

```
./sdrtrunk
```

Outcome: after unpacking a release archive for your OS, the bundled launch script starts sdrtrunk with its own Java runtime; the GUI opens and the tuner/channel configuration editor lets you add an SDR tuner, program the trunked system's control channel, and begin decoding calls to audio and events.
