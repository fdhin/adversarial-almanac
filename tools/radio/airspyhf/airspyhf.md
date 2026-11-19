---
name: "airspyhf"
categories:
  - blackarch-radio
version: "1.6.8.r75.g24fe8ff"
homepage: "https://airspy.com"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-08-02"
build-system: cmake
license: BSD
meta-checked: "2026-11-09"
github-repo: "airspy/airspyhf"
github-stars: 184
github-pushed: "2026-07-22"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 6.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 16
gh-activity-checked: "2026-11-09"
---

# airspyhf

> Host-side driver library and receiver command-line tools for the Airspy HF+ SDR.

## What it's for

This is the user-mode host software for the Airspy HF+, a software-defined radio covering 9 kHz–31 MHz and 60–260 MHz. It ships `libairspyhf` plus command-line utilities: `airspyhf_rx` streams raw float32 I/Q samples (768 kS/s) from the device to a file, WAV or stdout; `airspyhf_info` reports attached devices, serial numbers and firmware version; `airspyhf_calibrate` reads or sets the frequency calibration in parts per billion; `airspyhf_gpio` and `airspyhf_lib_version` round out the set. It is specific to the HF+ hardware family — other Airspy receivers (R2, Mini) use the separate airspyone_host stack.

## Typical use cases

### List attached devices and firmware

```
airspyhf_info
```

Outcome: prints the number of connected Airspy HF+ devices with each receiver's 64-bit serial number, firmware and library versions — grab a serial for `-s` when several devices are attached.

### Capture a fixed amount of I/Q to a file

```
airspyhf_rx -f 7.2 -r pass.f32 -n 7680000
```

Outcome: tunes to 7.2 MHz and writes exactly 10 million float32 samples (about 13 s at 768 kS/s) to `pass.f32`, then exits — `-n` bounds the otherwise unlimited transfer.

### Record broadcast FM to a WAV file

```
airspyhf_rx -f 107.9 -w -g on -l low
```

Outcome: tunes to 107.9 MHz with the HF AGC enabled at the low threshold and records receiver samples to a WAV file with an automatically generated name, ready to open in SDR software.

### Pipe live samples into a csdr demodulator chain

```
airspyhf_rx -f 107.9 -r stdout | csdr fmdemod_quadri_cf | csdr fractional_decimator_ff 16 | csdr deemphasis_wfm_ff 48000 50e-6 | csdr convert_f_s16 | mplayer -cache 1024 -quiet -rawaudio samplesize=2:channels=1:rate=48000 -demuxer rawaudio -
```

Outcome: streams the float32 baseband from the device through FM demodulation, 16× decimation to 48 kHz and 50 µs European de-emphasis into playable audio — the airspyhf_rx + csdr receive pattern from the upstream tools README.

### Read and correct frequency calibration

```
airspyhf_calibrate
airspyhf_calibrate -c 6
```

Outcome: the first invocation prints the stored calibration in parts per billion (`Calibration = <ppb>`); the second writes a new value of +6 ppb to the device, trimming its frequency error.

*No corpus evidence — derived from upstream documentation.*
