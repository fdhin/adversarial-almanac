---
name: "aptdec"
categories:
  - blackarch-radio
version: "v1.8.0.r2.g7db2030"
homepage: "https://github.com/Xerbo/aptdec"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2025-11-21"
build-system: cmake
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "Xerbo/aptdec"
github-stars: 90
github-pushed: "2023-11-30"
github-archived: true
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# aptdec

> Decode NOAA APT weather-satellite imagery from FM audio recordings.

## What it's for

Aptdec decodes the Analog Picture Transmission (APT) imagery continuously broadcast by NOAA polar-orbiting weather satellites (NOAA 15–19). Feed it the narrowband-FM audio of a satellite pass recorded with any SDR — WAV, FLAC or anything libsndfile reads — and it produces PNG images: the raw two-channel image, a single channel, a temperature-compensated IR image or a false-color palette image, with optional denoise, histogram equalization, precipitation overlay and flip for northbound passes. It can also decode live from a pipe. It does not track satellites or record audio; pair it with a receiver such as gqrx and a pass predictor. Originally by Thierry Leconte (F4DWV), maintained 2019–2022 by Xerbo.

## Typical use cases

### Decode a recorded pass

```
aptdec noaa19_pass.wav
```

Outcome: writes `noaa19_pass-r.png` next to the input — the raw APT image with both channels and telemetry intact (satellite defaults to NOAA 19).

### Extract a temperature image for a known satellite

```
aptdec -i t -s 18 noaa18_pass.wav
```

Outcome: applies NOAA 18 calibration coefficients to the IR channel and writes a temperature-compensated image instead of the raw one.

### Batch decode a directory of recordings

```
mkdir -p images && aptdec -d images *.wav
```

Outcome: decodes every WAV in the current directory and places each output image in `images/` (the directory must already exist).

### Denoise and flip a northbound pass

```
aptdec -i a -e df noaa19_pass.wav
```

Outcome: writes a denoised, vertically flipped Channel A image — effect letters stack inside one `-e` argument (`d` denoise, `f` flip for northbound passes), and `-e` can combine with any output type.

### Apply a false-color palette

```
aptdec -i p -p palettes/WXtoImg-N18-HVC.png noaa18_pass.wav
```

Outcome: colors the image by looking up Channel A (x axis) and Channel B (y axis) in the 256×256 palette PNG, the same way WXtoImg palettes behave.

### Realtime decode from live receiver audio

```
mkfifo /tmp/aptaudio
aptdec -r /tmp/aptaudio
sox -t pulseaudio alsa_output.pci-0000_00_1b.0.analog-stereo.monitor -c 1 -t wav /tmp/aptaudio
```

Outcome: aptdec progressively renders the image while the pass is still in progress; killing the sox process finalizes and calibrates the partial image.

*No corpus evidence — derived from upstream documentation.*
