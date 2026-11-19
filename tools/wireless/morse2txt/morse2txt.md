---
name: "morse2txt"
gentoo-category: "net-wireless"
version: "1.0.0"
homepage: "http://morse2txt.sourceforge.net/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: make
license: GPL-2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# morse2txt

> GTK+ application that decodes Morse code audio from a sound card into text in real time.

*No corpus evidence — derived from upstream documentation.*

## What it's for

morse2txt (by Ken Prox) listens to a shortwave receiver connected to the sound card (OSS or ALSA backend), detects the tone with FFT-based filtering plus automatic gain control, adapts to the operator's sending speed, and continuously prints the decoded Morse as text — the author rates it down to 20 dB signal-to-noise. It is a GUI program for ham-radio and monitoring use: there is no command-line parsing mode, and it decodes live audio only, not recorded files.

## Typical use cases

### Decode live Morse audio

Tune a receiver to a CW station and read the text as it is sent.

```
morse2txt
```

Outcome: opens the GTK+ window; select the audio device and tone parameters, and decoded characters stream into the text view while the signal is present.
