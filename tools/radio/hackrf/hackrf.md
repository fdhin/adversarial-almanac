---
name: "hackrf"
categories:
  - sdr
version: "2026.01.3-1"
homepage: "https://github.com/greatscottgadgets/hackrf"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-01-31"
build-system: cmake
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "greatscottgadgets/hackrf"
github-stars: 8203
github-pushed: "2026-11-06"
github-archived: false
github-issues: 86
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 466
gh-activity-checked: "2026-11-09"
---

# hackrf

> Host-side command-line utilities for the HackRF One software-defined radio.

## What it's for

The userland half of the HackRF platform: a set of small tools that identify the attached device, move IQ samples to and from files or pipes, sweep the spectrum, flash firmware, and tweak low-level hardware state. Covers 1 MHz to 6 GHz, half-duplex, with 8-bit signed complex samples (`.cs8`) at 2–20 Msps. These tools are the foundation most other HackRF workflows (gr-gsm, inspectrum captures, replay attacks in a lab) are built on — they capture and replay raw IQ; demodulation and protocol decoding happen in other software. There is no separate "tune" utility: the centre frequency is chosen per operation with `-f` on the transfer/sweep tools. Transmissions are only legal on frequencies you are licensed to use.

Core commands installed by the package: `hackrf_info`, `hackrf_transfer`, `hackrf_sweep`, `hackrf_spiflash`, `hackrf_debug`, `hackrf_biast`, `hackrf_clock`, `hackrf_operacake`, `hackrf_cpldjtag`.

## Typical use cases

### Verify the device is alive

Confirm enumeration, firmware version and serial number before anything else.

```
hackrf_info
```

Outcome: prints one block per connected HackRF with board model, firmware version and serial number; a "no HackRF boards found" message (or USB permission errors) means udev rules or the cable are the problem, not the software. With several devices attached, the printed serials feed the `-d serial` option every other tool accepts to pick one.

### Capture a band of spectrum to a file

Record raw IQ at a fixed centre frequency for later analysis.

```
hackrf_transfer -f 433920000 -s 8000000 -l 32 -g 32 -r capture.cs8
```

Outcome: the receiver tunes to 433.92 MHz at 8 Msps and writes interleaved signed 8-bit I/Q to `capture.cs8` until interrupted with Ctrl-C, printing throughput and dropped-buffer counts. `-l` sets the RX LNA/IF gain (0–40 dB, 8 dB steps) and `-g` the baseband VGA gain (0–62 dB, 2 dB steps); adjust both so the signal occupies the middle of the dynamic range. The file opens directly in inspectrum or feeds a GNU Radio flow graph.

### Transmit or replay an IQ file

Send a previously captured or synthesized signal on air (into a load or licensed band).

```
hackrf_transfer -f 433920000 -s 8000000 -x 40 -t capture.cs8
```

Outcome: the file's samples stream to the transmitter at the given frequency and sample rate, looping until stopped; `-x` sets TX VGA gain (0–47 dB). Feed `-t -` to pipe samples from another process (e.g. `hacktv` or a GNU Radio sink) instead of a file. Combined with the capture command this is the standard record/replay pair for RF analysis.

### Generate a continuous-wave carrier

Use the transfer tool itself as a signal source.

```
hackrf_transfer -f 433920000 -s 8000000 -c 100
```

Outcome: transmits a constant DC level (CW carrier at the centre frequency) with amplitude 100 of 0–127 until interrupted — handy for antenna testing, gain staging and checking a receiver's frequency calibration without preparing an IQ file.

### Sweep a wide frequency range as a spectrum overview

Produce CSV power readings across hundreds of MHz in seconds.

```
hackrf_sweep -f 100:600 -w 1000000
```

Outcome: continuously sweeps 100–600 MHz and prints one CSV line per sweep segment (`date, time, hz_low, hz_high, hz_bin_width, num_samples, dB, dB, ...`) to stdout — pipe it to a file or a plotting tool. `-w` sets FFT bin width in Hz (2445–5 000 000), `-1` performs a single pass and exits, `-N` limits the sweep count, `-B` switches to binary output. RX gains use the same `-l`/`-g` flags as capture.

### Flash new firmware

Write a firmware image to the HackRF's SPI flash.

```
hackrf_spiflash -w firmware.bin
```

Outcome: erases and writes the image to flash and verifies the device type compatibility; after a reboot `hackrf_info` reports the new firmware version. `-r dump.bin` reads flash back for inspection, `-d serial` picks the device when several are attached. This is the standard step after downloading a firmware release.

### Power an antenna from the bias tee

Enable the bias tee for powered antennas and LNAs.

```
hackrf_biast -b 1
```

Outcome: switches the antenna-port power on immediately without a transfer in progress (0 disables); `-r`/`-t`/`-o on|off|leave` additionally set what the port does automatically on entering RX, TX and OFF modes. Useful with SDR software that has no bias-tee support of its own.

*No corpus evidence — derived from upstream documentation.*
