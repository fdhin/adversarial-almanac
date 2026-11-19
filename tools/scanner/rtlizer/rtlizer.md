---
name: "rtlizer"
categories:
  - blackarch-scanner
version: "2.1.r1.g5614163"
homepage: "https://github.com/csete/rtlizer"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-14"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "csete/rtlizer"
github-stars: 133
github-pushed: "2015-04-29"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rtlizer

> Minimal GTK+ real-time spectrum analyzer for RTL-SDR dongles, by Alexandru Csete.

## What it's for

A deliberately small spectrum analyzer: it feeds an RTL-SDR dongle through
kiss_fft and draws a live spectrum on a GTK+ 3 window — originally written as a
Beaglebone + RTL-SDR demo, it runs on anything with rtl-sdr and Gtk+
installed, including a Raspberry Pi with a small TFT display. Use it to eyeball
a band: find where a signal lives, check occupied bandwidth, or watch a
frequency range while tuning other SDR software. It has no demodulation,
recording, or annotation features — for full-featured SDR reception use
something like gqrx (same author); rtlizer is the quick "what's on the
spectrum" display. The upstream repository is archived (last push 2015), so the
tool is stable but unmaintained, and it ships no license file.

## Typical use cases

### Open the live spectrum display

Plug in a dongle and start the analyzer.

```
rtlizer
```

Outcome: a 320x240 spectrum window opens showing the default center frequency;
tune with the arrow keys — left/right decrease/increase frequency, up/down
change the sample rate — and press Enter to quit. Use it to spot active
transmissions around the current center frequency at a glance.

### Launch with a custom window geometry

Size and position the window for a larger display or embedded panel.

```
rtlizer 640x360+0+0
```

Outcome: the same spectrum view opens with the X11 geometry applied (640x360 at
the top-left corner) instead of the default 320x240 — handy on Raspberry Pi
piTFT-style screens or when tiling it next to other SDR tools; further options
are listed by `rtlizer -h`.

*No corpus evidence — derived from upstream documentation (csete/rtlizer
README — usage and key bindings).*
