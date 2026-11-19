---
name: "gr-paint"
categories:
  - blackarch-radio
version: "53.c443e36"
homepage: "https://github.com/drmpeg/gr-paint"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: cmake
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "drmpeg/gr-paint"
github-stars: 144
github-pushed: "2025-08-24"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gr-paint

> OFDM spectrum painter — transmits monochrome images that appear in a receiver's waterfall.

## What it's for

Rendering pictures on the waterfall display of any SDR receiver: it converts a monochrome image into a 4K-IFFT OFDM IQ sequence whose instantaneous spectrum draws the image line by line, so anyone watching a waterfall (gqrx, SDR#, etc.) on the transmit frequency sees the picture scroll through. Based on the spectrum_painter concept, packaged as a GNU Radio block named "Spectrum Painter" (category "Paint") with two parameters — Image Width (horizontal size in pixels) and Line Repeats (aspect-ratio control). It is a demonstration/transmit-side toy, not a measurement tool; it does nothing to the receive side except be visible there.

After installation the block is available in GNU Radio Companion, a ready-made transmit flow graph (`paint_tx.grc`, plus an audio-based variant) ships in the apps directory, and the `tgatoluma` helper converts images to the raw binary stream the block consumes.

## Typical use cases

### Convert an image to a raw luma file for transmission

Prepare any picture as TGA, flip it for top-down waterfalls, then convert to the raw binary format the Spectrum Painter block reads.

```
convert -flip image.png image.tga && tgatoluma image.tga image.bin
```

Outcome: `image.bin` contains the raw image byte stream, and `tgatoluma` prints the image's horizontal size in pixels — enter that number into the block's "Image Width" parameter. ImageMagick (`convert`) produces the TGA; the flip is needed because waterfalls typically start at the top.

### Transmit the image into a receiver's waterfall

Open the provided flow graph and point its sink at your SDR.

```
gnuradio-companion paint_tx.grc
```

Outcome: the flow graph opens with the Spectrum Painter block fed by a file source and an SDR sink (default set up for an Ettus B2x0 — swap in the sink block matching your device). Load `image.bin` (or one of the bundled test images `marcy.bin`, `ramp.bin`, `xhatch.bin`), set Image Width to the printed width, adjust Line Repeats until the aspect ratio looks right on a distant waterfall, then transmit. Bundled `ramp.bin` sets contrast and `xhatch.bin` sets aspect ratio.

### Transmit directly from a PNG without pre-conversion

Use the Image File Source block (added upstream to the provided flow graph) as an alternative to the ImageMagick/tgatoluma steps.

```
gnuradio-companion paint_tx.grc
```

Outcome: with the Image File Source block in place of the raw file source, any image format can be loaded directly (a test PNG is bundled with the project); the block prints the image width so you can type it into the Spectrum Painter block's Image Width parameter, skipping the conversion step entirely. An `paint_tx_audio.grc` variant is also included for audio-based output.

*No corpus evidence — derived from upstream documentation.*
