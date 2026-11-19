---
name: "hacktv"
categories:
  - blackarch-radio
version: "543.80d98ea"
homepage: "https://github.com/fsphil/hacktv"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-21"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "fsphil/hacktv"
github-stars: 2
github-pushed: "2026-10-12"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# hacktv

> Analogue TV transmitter for the HackRF and other SDRs, by Philip Heron.

## What it's for

Generating and transmitting retro analogue television: it takes any input ffmpeg can read (file, URL, capture device) or a built-in test pattern and modulates it as PAL, NTSC, SECAM or D/D2-MAC — plus historic 819, 405, 240 and 30-line standards and the NASA Apollo video standards. Output goes to a HackRF, an fl2k VGA adaptor, any SDR via SoapySDR, or a raw baseband file. Extras include teletext (625-line), NICAM stereo audio, Videocrypt I/II/S and partial Nagravision Syster scrambling support, and an analogue copy-protection mode. It will not produce DVB or other digital broadcast signals. The HackRF is not designed to connect directly to AV equipment — mind DC and RF power levels, and transmit only on frequencies you may legally use.

The project's GitHub repository (fsphil/hacktv) is archived; active development continues at https://codeberg.org/fsphil/hacktv, with the project page at https://sanslogic.co.uk/hacktv.

## Typical use cases

### Render a video into a PAL baseband file

Convert any video into an analogue-modulation IQ/baseband file without transmitting.

```
hacktv -o baseband.bin -m pal example.mkv
```

Outcome: decodes `example.mkv` through ffmpeg, modulates it as PAL, and writes the baseband signal to `baseband.bin` — ready to inspect in a tool like inspectrum or to replay later. `-m` selects the standard (pal, ntsc, secam, the historic modes, or system letters like `-m i` for PAL-I).

### Broadcast a test pattern on a UHF channel

Radiate a clean analogue carrier to align a receiver or antenna.

```
hacktv -f 551250000 -m i -g 47 test
```

Outcome: transmits the internal test pattern as PAL System I centred on 551.25 MHz (UHF channel 31 style frequency) with 47 dB TX gain from the HackRF; an analogue-capable receiver or SDR waterfall tuned there shows the bars. `-f` sets the centre frequency, `-g` the transmit gain.

### Transmit video with teletext

Carry teletext pages alongside the programme.

```
hacktv -f 551250000 -m i -g 47 --teletext demo.tti test
```

Outcome: the teletext pages described by the `.tti` file are inserted into the vertical blanking interval, so receivers with teletext decoders (or a dedicated teletext decoder fed from the SDR) render page 100 and beyond while the test pattern plays.

*No corpus evidence — derived from upstream documentation.*
