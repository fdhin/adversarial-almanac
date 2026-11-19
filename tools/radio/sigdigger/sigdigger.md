---
name: "sigdigger"
categories:
  - radio
homepage: "https://github.com/BatchDrake/SigDigger"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: other
license: LGPL3
github-repo: "BatchDrake/SigDigger"
github-stars: 2944
github-match: "exact"
gh-checked: "2026-11-13"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
---

# sigdigger

> Free Qt digital signal analyzer for unknown RF signals — live spectrum/waterfall, channel inspection and adjustable FSK/PSK/ASK demodulation over any SoapySDR device, built for signal reverse engineering.

## What it's for

SigDigger (Gonzalo José Carracedo Carballal, BatchDrake) is a free graphical digital signal analyzer for GNU/Linux and macOS whose main use case is reverse engineering unknown radio signals. It talks to most consumer SDR receivers through SoapySDR, plays back and records raw IQ captures, and does its DSP through the Suscan/Sigutils libraries rather than GNU Radio — so despite the deliberately Gqrx-like spectrum UI, it is a different animal: beyond live listening it offers real-time channel inspection (constellations, parameter estimation, clock recovery for FSK/PSK/ASK), a deferred Time window for offline waveform measurement and sample export, panoramic wideband sweeps, and plugin extensions (e.g. an APT weather-satellite decoder). It is GUI-only — there is no CLI surface beyond launching the binary — and it analyzes; it does not transmit or attack, so pair it with a transmit-capable framework when a finding must become an interaction.

## Typical use cases

### Configure a source and start capturing (UI workflow)

1. Toolbar → Settings (Ctrl+S) → Source tab.
2. Select the `SDR device` radio button → pick your device in the `Device` drop-down → set sample rate/frequency.
3. OK → toolbar → Start/Stop capture.

Outcome: live spectrum and waterfall appear as samples flow; the same Settings → Source tab with the `File source` radio button and `Browse...` replays a previously captured IQ file instead, and the Capture recorder in the source panel can write the live baseband back to disk for later analysis.

### Define a channel on a signal of interest (UI workflow)

Drag the green filter box (the shaded rectangle with the vertical red line) on the spectrum so it frames the signal's bandwidth.

Outcome: the boxed span becomes a channel centered for inspection — its width sets the sample rate the inspector DSP chain will see (keep it near the signal's bandwidth so symbols stay a few samples long); middle-button drag on the spectrum retunes the receiver center.

### Inspect a channel in real time (UI workflow)

1. With the channel defined, choose the `Inspector type` in the drop-down (the built-in `Generic Channel Inspector` is always available).
2. Click `Open inspector`.
3. Work the side-panel groups: Channel parameters, Parameter estimation, Demodulator controls, Subcarrier inspection, Data forwarding.

Outcome: an inspection tab opens with sampling plots and analysis panes for that channel — run the parameter estimators to get symbol rate/baud and modulation facts, then the demodulator controls to lock clock and carrier and read out bits for FSK/PSK/ASK signals.

### Measure an offline capture in the Time window (UI workflow)

Open the Time window on the capture → Measurements page → enable Periodic selection and set the number of cycles.

Outcome: the selection is divided into periodic subintervals (red vertical lines), so fitting several symbols instead of one yields an accurate symbol-rate reading from `Selection freq`; the same window saves the selected samples as WAV, MATLAB `.m` script or MAT-file, and its Carrier recovery page (Automatic frequency correction → Sync on a sync tone) zeroes residual frequency error before phase-sensitive measurements.

### Forward demodulated samples to another tool (UI workflow)

Inspector tab → side panel → Data forwarding → Network forwarding → set Connection type (TCP or UDP), host and port → Forward.

Outcome: SigDigger streams the demodulated sample stream to your listener (e.g. a custom decoder or GNU Radio flowgraph) as soon as the connection opens — useful for making it one stage of a longer DSP chain; the same group can instead record demodulated audio to `channel-capture-<Mod>-<Rate>baud-NNNN.wav` files.

### Sweep a wide band panoramically (UI workflow)

View menu → Panoramic spectrum (Ctrl+P) → set device, frequency range, sample rate and hop speed.

Outcome: SigDigger hops the tuner across the range, gluing each dwell's spectrum into one wide panoramic display — the fast way to find where unknown emitters live before defining channels on them.

No corpus evidence — derived from upstream documentation.
