---
name: "recordmydesktop"
categories:
  - reporting
version: "0.4.0-4"
homepage: "https://github.com/Enselic/recordmydesktop/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali]
arch-builddate: "2025-04-19"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "Enselic/recordmydesktop"
github-stars: 80
github-pushed: "2023-05-09"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# recordmydesktop

> Command-line screen recorder for X11 Linux desktops that produces Ogg Theora video with optional Vorbis audio.

## What it's for

recordMyDesktop captures a rectangular area of an X display (size and offset via `--width`, `--height`, `-x`, `-y`, or a window id) together with ALSA/OSS/JACK audio, and encodes the result to an `.ogv` file (Theora video, Vorbis sound). Video quality is tuned with `--v_quality` (0–63) and `--v_bitrate`, and `--on-the-fly-encoding` encodes while recording instead of after. It is X11-only — it does not work on Wayland sessions — and the project is dormant (last release 0.4.0), though still packaged by Arch and Kali. GUI frontends (`gtk-recordMyDesktop`, `qt-recordmydesktop`) exist as separate packages that wrap this binary.

## Typical use cases

### Record the desktop with defaults

Capture a quick clip without tuning anything.

```
recordmydesktop -o session.ogv
```

Outcome: records the default capture area (a window selection if no dimensions are given) to `session.ogv` and encodes it when recording stops; Ctrl-C in the terminal finalizes the file.

### Record a fixed region at a set frame rate

Capture a chosen rectangle instead of a window.

```
recordmydesktop -o demo.ogv --width 1280 --height 720 -x 0 -y 0 --fps 30
```

Outcome: records the 1280x720 rectangle at offset (0,0) at 30 frames per second to `demo.ogv`.

### Silent, high-quality capture

Skip audio and maximize video quality.

```
recordmydesktop -o demo.ogv --no-sound --v_quality 63 --v_bitrate 2000000
```

Outcome: disables audio capture entirely and encodes at the top of the 0–63 quality range with a 2,000,000 bitrate target; `--v_bitrate` must stay within 0–2000000.

### Encode while recording

Avoid the long post-recording encoding pass.

```
recordmydesktop -o demo.ogv --on-the-fly-encoding
```

Outcome: interleaves encoding with capture, so the file is essentially ready when recording stops (at some cost to capture performance).

### Start recording after a delay

Give yourself time to set up the screen being captured.

```
recordmydesktop -o demo.ogv --delay 10
```

Outcome: waits 10 seconds before capture starts; the delay accepts `n[H|h|M|m]` suffixes for hours or minutes (e.g. `--delay 2M`).

*No corpus evidence — derived from upstream documentation.*
