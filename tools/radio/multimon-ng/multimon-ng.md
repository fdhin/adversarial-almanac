---
name: "multimon-ng"
categories:
  - sdr
version: "1.6.2-1"
homepage: "https://github.com/EliasOenal/multimon-ng"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-10-03"
build-system: cmake
license: GPL-2.0-only
meta-checked: "2026-11-09"
github-repo: "EliasOenal/multimon-ng"
github-stars: 1147
github-pushed: "2026-10-19"
github-archived: false
github-issues: 70
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 41
gh-activity-checked: "2026-11-09"
---

# multimon-ng

> Decoder for digital radio signalling modes — POCSAG, FLEX, EAS, AFSK, DTMF, selcall and more — fed by audio files or a live SDR pipe.

*No corpus evidence — derived from upstream documentation.*

## What it's for

multimon-ng (successor of multimon) demodulates digital transmission modes carried in voice-band audio: pager protocols (POCSAG512/1200/2400, FLEX, FLEX_NEXT), EAS, a family of AFSK/FSK modes, DTMF, analog siren dual-tone and ZVEI/EEA/EIA/CCIR selcall sequences, and MORSE_CW. Input is 22050 Hz signed 16-bit raw samples from a file, stdin, a sound card, or a pipe from an SDR tool such as `rtl_fm`; the common pattern is tuning a cheap receiver to a pager or signalling channel and turning the audio straight into text. It decodes only — it does not receive RF itself and does not transmit.

## Typical use cases

### Decode a pager channel live from RTL-SDR

```
rtl_fm -f 439.9875M -s 22050 | multimon-ng -t raw -a POCSAG1200 /dev/stdin
```

Outcome: rtl_fm tunes the dongle and pipes 22050 Hz raw audio; multimon-ng decodes POCSAG1200 and prints each message (address, function bits, alphanumeric text) to the terminal as long as the channel is active.

### Decode a WAV recording through sox

```
sox -R -t wav pagers.wav -esigned-integer -b16 -r 22050 -t raw - | multimon-ng -a POCSAG512 -a FLEX -
```

Outcome: sox converts the WAV into multimon-ng's native raw format on stdout; the trailing `-` makes multimon-ng read from stdin and decode both selected demodulators at once, so one pass recovers whatever protocol the recording contains.

### Replay a saved FLAC capture quietly

```
multimon-ng -r -v 0 -a FLEX_NEXT -t flac recordings/pagers.flac
```

Outcome: decodes FLEX_NEXT directly from the FLAC file (`-t flac`), `-v 0` suppresses the per-packet verbosity, and `-r` outputs the raw undecoded data for later reprocessing.

### Demodulate multiple signalling modes from one stream

```
rtl_fm -f 403.6M -s 22050 | multimon-ng -t raw -a FMSFSK -a AFSK1200 /dev/stdin
```

Outcome: both FMSFSK and AFSK1200 demodulators run concurrently over the same tuned channel; any matching frame from either mode is printed, which is useful when a band carries mixed services.
