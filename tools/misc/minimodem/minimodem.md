---
name: "minimodem"
categories:
  - blackarch-misc
version: "353.bb2f34c"
homepage: "https://github.com/kamalmostafa/minimodem"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2022-08-28"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "kamalmostafa/minimodem"
github-stars: 984
github-pushed: "2024-04-17"
github-archived: false
github-issues: 33
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# minimodem

> General-purpose software audio FSK modem that decodes or generates modem tones at any baud rate, for Bell, V.21, RTTY, NOAA SAME, and Caller-ID protocols.

## What it's for

Moving data over audio. minimodem is a command-line FSK modem: it generates (`--tx`) or decodes (`--rx`) audio modem tones at any specified baud rate using framing protocols such as Bell202 1200 bps ASCII, Bell103 300 bps, ITU-T V.21, RTTY/TTY-TDD baudot, NOAA SAME emergency alerts, Caller-ID (MDMF/SDMF), and UIC-751-3 railway messaging. It works in real time through the system audio device or in batch mode against audio files (format set by the file extension), which makes it useful for decoding recorded telemetry, radio or phone-line captures, and for transferring data between computers over a sound cable, radio, or any other audio medium. It does not decode AX.25 framed packets, supports no modem "AT" control commands, and does not produce DTMF dialing tones.

## Typical use cases

### Decode a recorded FSK audio file

Extract the data hidden in a captured audio recording.

```
minimodem --rx -f recording.wav 1200
```

Outcome: decodes the WAV as Bell202 1200 bps ASCII and prints the recovered text to stdout, with CARRIER/NOCARRIER and signal-analysis metrics (add `-q` to silence them).

### Transmit text into an audio file

Render data as FSK tones for later playback or a round-trip test.

```
echo "test message" | minimodem --tx -f message.wav 300
```

Outcome: writes message.wav containing Bell103 300 bps FSK tones of the piped text; another host running `minimodem --rx -f message.wav 300` recovers it.

### Decode amateur radio RTTY from live audio

Listen to a live RTTY transmission through the sound card.

```
minimodem --rx -a rtty
```

Outcome: opens the default system audio device and continuously decodes 45.45 baud baudot RTTY; `-a` auto-detects the mark and space tone frequencies from the carrier.

### Decode NOAA SAME emergency alert tones

Recover the text of a recorded emergency alert broadcast.

```
minimodem --rx same -f alert.wav
```

Outcome: decodes the SAME 520.83 bps protocol (0xAB sync byte, no start/stop bits) from the recording and prints the alert message frames.

### Tune reception of noisy signals

Control how strict the decoder is about signal quality.

```
minimodem --rx -c 5.0 -f noisy.wav 300
```

Outcome: raises the SNR-based confidence threshold from the default 1.5 to 5.0 so only very clean Bell103 frames decode; lower it toward 1.0 to get partial decodes from noisy captures.

*No corpus evidence — derived from upstream documentation.*
