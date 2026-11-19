---
name: "dsd"
categories:
  - blackarch-misc
version: "v1.6.0.r96.g59423fa"
homepage: "https://github.com/szechyjs/dsd"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-12-04"
build-system: cmake
license: GPL-2.0-only
meta-checked: "2026-11-09"
github-repo: "szechyjs/dsd"
github-stars: 791
github-pushed: "2025-04-06"
github-archived: false
github-issues: 68
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dsd

> The classic Digital Speech Decoder: decodes P25 Phase 1, DMR/MOTOTRBO, D-STAR, NXDN, X2-TDMA and ProVoice from discriminator-tap audio and synthesizes the speech.

## What it's for

DSD (the szechyjs-maintained branch of the original tool) decodes digital-voice formats from a scanner's discriminator-tap audio — live from an audio device, from piped stdin, or from saved MBE data — and synthesizes the speech (synthesis requires the separate mbelib). It is the lean, stable original: frame info and error bars on a terminal, wav output of synthesized audio, MBE data dumping for replay, and a datascope for checking signal quality. Auto-detection covers P25P1, DMR/MOTOTRBO, NXDN96, D-STAR and X2-TDMA; NXDN48/IDAS and ProVoice cannot be auto-detected and need their explicit `-f` flag.

For RTL-SDR input, trunking, an NCurses UI and modern protocol additions (P25 Phase 2, YSF, M17, dPMR, EDACS), use its actively developed fork `dsd-fme`.

## Typical use cases

### Live decode from the sound card

Feed a discriminator tap or SDR demodulator output from the default audio device.

```
dsd -fr
```

Outcome: live scanner mode decoding only DMR/MOTOTRBO (`-fr`); use `-fa` for auto-detection (the default) or `-f1`/`-fd`/`-fn`/`-fx` to lock P25 Phase 1, D-STAR, NXDN96 or X2-TDMA. Frame info and error bars render continuously; matching `-m` flags (C4FM/GFSK/QPSK optimizations) can improve marginal signals.

### Decode piped audio to a wav file

Process a recording through stdin and keep the synthesized speech.

```
dsd -i - -f1 -w decoded.wav < recording.raw
```

Outcome: reads sampled audio from stdin (`-` selects piped input instead of the audio device), decodes P25 Phase 1, and writes the synthesized speech to decoded.wav (`-w`) while printing frame sync and error levels to the terminal.

### Capture MBE data and replay it later

Record the raw voice-frame data during reception, then re-decode it offline.

```
dsd -fr -d mbe-out
dsd -r mbe-out/* -w replay.wav
```

Outcome: the first command writes each voice frame's MBE data into the mbe-out directory while decoding live; the second replays those saved MBE files through the synthesizer, producing replay.wav — useful for re-processing traffic at different unvoiced-quality settings (`-u`) without re-receiving.

### Check signal quality with the datascope

See the symbol stream instead of decoding.

```
dsd -s
```

Outcome: replaces the frame display with a datascope of the received symbols (`-z <num>` adjusts its frame rate), used to verify discriminator-tap level and quality before attempting decode.

*No corpus evidence — derived from upstream documentation.*
