---
name: "csdr"
categories:
  - blackarch-radio
version: "0.18.2.r68.g1f15b8c"
homepage: "https://github.com/jketterl/csdr"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2024-11-24"
build-system: cmake
license: GPL-3.0
meta-checked: "2026-11-09"
github-repo: "jketterl/csdr"
github-stars: 68
github-pushed: "2024-07-11"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# csdr

> Command-line DSP building blocks that turn SDR receive chains into shell pipelines.

## What it's for

csdr exposes DSP routines as Unix filters: each `csdr <function> [parameters]` reads samples on stdin, processes them and writes results to stdout, so complete software-defined-radio receive chains — type conversion, frequency shift, FIR filtering, decimation, FM/AM/SSB demodulation, de-emphasis, AGC, FFT — are assembled with pipes. It was written by András Retzler (HA7ILM) as the DSP engine of OpenWebRX and is now maintained at jketterl/csdr; `csdr -h` or the README's command index lists every function with its parameters. csdr handles only the processing — the sample source (an SDR like rtl_sdr) and audio playback (mplayer, aplay) come from other tools.

## Typical use cases

### Demodulate wideband FM and listen live

```
rtl_sdr -s 240000 -f 89500000 -g 20 - | csdr convert_u8_f | csdr fmdemod_quadri_cf | csdr fractional_decimator_ff 5 | csdr deemphasis_wfm_ff 48000 50e-6 | csdr convert_f_s16 | mplayer -cache 1024 -quiet -rawaudio samplesize=2:channels=1:rate=48000 -demuxer rawaudio -
```

Outcome: converts the dongle's u8 samples to float, quadrature-FM-demodulates the lone station at 89.5 MHz, decimates by 5 to 48 kHz, applies 50 µs European de-emphasis and plays the audio — the minimal broadcast-FM receive chain.

### Pull one station out of a crowded band

```
rtl_sdr -s 2400000 -f 89300000 -g 20 - | csdr convert_u8_f | csdr shift_addition_cc -0.085 | csdr fir_decimate_cc 10 0.05 HAMMING | csdr fmdemod_quadri_cf | csdr fractional_decimator_ff 5 | csdr deemphasis_wfm_ff 48000 50e-6 | csdr convert_f_s16 | mplayer -cache 1024 -quiet -rawaudio samplesize=2:channels=1:rate=48000 -demuxer rawaudio -
```

Outcome: shifts the target station (−0.085 of the sample rate ≈ −204 kHz, i.e. onto 89.5 MHz) to the center, decimates by 10 with a Hamming-windowed FIR, then demodulates — the shift + filter + demodulate pattern for wide captures with multiple stations.

### Demodulate narrowband FM with AGC

```
rtl_sdr -s 2400000 -f 145000000 -g 20 - | csdr convert_u8_f | csdr shift_addition_cc 0.14583333 | csdr fir_decimate_cc 50 0.005 HAMMING | csdr fmdemod_quadri_cf | csdr limit_ff | csdr deemphasis_nfm_ff 48000 | csdr fastagc_ff | csdr convert_f_s16 | mplayer -cache 1024 -quiet -rawaudio samplesize=2:channels=1:rate=48000 -demuxer rawaudio -
```

Outcome: selects a ~25 kHz narrowband channel 600 kHz above the center (145.35 MHz) with a 50× decimating FIR, demodulates NFM with fixed 400–4000 Hz de-emphasis and fast AGC — the pattern for amateur and commercial narrowband channels.

### Demodulate AM and SSB

```
rtl_sdr -s 2400000 -f 145000000 -g 20 - | csdr convert_u8_f | csdr shift_addition_cc 0.25 | csdr fir_decimate_cc 50 0.005 HAMMING | csdr bandpass_fir_fft_cc 0 0.1 0.05 | csdr realpart_cf | csdr agc_ff | csdr limit_ff | csdr convert_f_s16 | mplayer -cache 1024 -quiet -rawaudio samplesize=2:channels=1:rate=48000 -demuxer rawaudio -
```

Outcome: Weaver-style SSB demodulation — the complex bandpass keeps only the upper sideband (`bandpass_fir_fft_cc 0 0.1`); for LSB use `-0.1 0`, and for plain AM replace the bandpass and `realpart_cf` with `amdemod_cf fastdcblock_ff` followed by the same AGC tail.

### Plot the spectrum of a live signal

```
rtl_sdr -s 2400000 -f 104300000 -g 20 - | csdr convert_u8_f | csdr fft_cc 1024 1200000 HAMMING --octave | octave -i > /dev/null
```

Outcome: computes a 1024-point Hamming-windowed FFT roughly twice per second and pipes octave commands to a live GNU Octave spectrum plot — a quick way to see what a band contains before building a receive chain.

### Design and inspect an FIR filter

```
csdr firdes_bandpass_c 0 0.5 59 HAMMING --octave | octave -i
```

Outcome: designs a 59-tap complex bandpass that passes only positive frequencies (edges are relative to the sample rate) and plots its frequency response in Octave; drop `--octave` and everything after it to print the raw taps for use in other tools.

*No corpus evidence — derived from upstream documentation.*
