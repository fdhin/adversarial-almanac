---
name: "inspectrum"
categories:
  - sdr
version: "0.4.0-1"
homepage: "https://github.com/miek/inspectrum"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-03-04"
build-system: cmake
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "miek/inspectrum"
github-stars: 2539
github-pushed: "2025-12-06"
github-archived: false
github-issues: 61
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 10
gh-activity-checked: "2026-11-09"
---

# inspectrum

> Off-line spectrum analyzer for captured SDR signals.

## What it's for

Making sense of IQ recordings: it renders a captured signal as a zoomable, pannable spectrogram (handling 100 GB+ files) with synchronized plots of amplitude, frequency, phase and raw IQ samples. It is the go-to tool between capturing a signal (hackrf_transfer, an SDR receiver, GNU Radio) and demodulating it: drag cursors to measure symbol periods and symbol rates, then export exactly the samples, a filtered slice, or symbols extracted with a chosen constellation for a decoder. It works strictly on files — it cannot tune or record a live SDR itself, so pair it with whatever capture tool your hardware provides.

Virtually every common SDR sample format is recognized: `.cs8`/`.c8` (HackRF), `.cu8`/`.u8` (RTL-SDR), `.cs16` (BladeRF), `.cf32`/`.cfile` (GNU Radio, osmocom_fft), `.cs32` (SDRAngel), `.cf64`/`.f64` (MATLAB), and SigMF `.sigmf-meta`/`.sigmf-data` pairs. Unknown extensions are read as `.cf32`; 64-bit samples are truncated to 32-bit internally.

## Typical use cases

### Open a HackRF capture and measure a signal's timing

Inspect what `hackrf_transfer -r capture.cs8` produced.

```
inspectrum capture.cs8
```

Outcome: a window opens with the spectrogram of the whole recording; scroll/zoom to the signal of interest and add symbol cursors (and a time plot) via the sidebar. Dragging two cursors onto consecutive symbols makes the status bar report the symbol period and rate — the numbers you need to configure a downstream demodulator.

### Extract symbols or a filtered sample slice for decoding

Once a burst is identified, select its time range and export just the payload.

```
inspectrum capture.cs8
```

Outcome: with a time range selected and a symbol plot added, choose a constellation for the symbol sink and save the extracted symbols to a file; alternatively export the selected time period or filtered samples (`*.cf32`) for replay or further processing in GNU Radio — this is the standard hand-off from visual analysis to a decoder.

### Review a SigMF recording with its metadata

Open an annotated recording captured by SigMF-aware software.

```
inspectrum recording.sigmf-meta
```

Outcome: the paired `.sigmf-data` samples load together with the recording's metadata rather than relying on the file extension, so channel captures exported from other tools display with the same zoom/plot/cursor workflow as native captures.

*No corpus evidence — derived from upstream documentation.*
