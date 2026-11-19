---
name: "timegen"
categories:
  - blackarch-wireless
version: "0.4"
homepage: "https://bastianborn.de/radio-clock-hack/"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-12"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# timegen

> Generates a WAV file carrying a chosen DCF77 time signal for re-setting radio-controlled clocks.

## What it's for

timegen (Bastian Born, proof of concept for his "radio clock hack")
renders the DCF77 longwave time code — 77.5 kHz amplitude modulation
carrying date and time minute by minute — into a 16-bit mono 192 kHz
WAV file. The workflow: generate the signal for a moment of your
choosing, feed it to a 77.5 kHz coupling (for example a ferrite
antenna near the clock), and a DCF77-compatible radio clock accepts
the bogus time as authoritative — useful for testing how
time-dependent devices behave under attacker-chosen time. Its limit is
exactly its purpose: it only produces the audio file, it does not
transmit anything. The author warns that emitting these signals may
not be legal in your country.

## Typical use cases

### Generate a DCF77 signal for a target time

Produce the WAV file to feed your coupling hardware.

```
timegen -o dcf77.wav -t 2026-06-01.12:00 -m 10
```

Outcome: prints "Starting flux capacitor... This may take a few
seconds." followed by "Done. File written: dcf77.wav" — ten minutes of
the DCF77 code starting at the given time (`-t` format
`YYYY-MM-DD.hh:mm`). A wall clock typically needs three to four
minutes of signal before it syncs, so generate at least that many
with `-m`; run with no arguments to see the usage banner.

*No corpus evidence — derived from the upstream source (bastianborn.de timegen 0.4 tarball: usage text in main.c; compiled and executed to verify the flags and output).*
