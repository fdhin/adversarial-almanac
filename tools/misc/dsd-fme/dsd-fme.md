---
name: "dsd-fme"
categories:
  - blackarch-misc
version: "2024"
homepage: "https://github.com/lwvmobile/dsd-fme"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-11-07"
build-system: cmake
license: custom:copyright
meta-checked: "2026-11-09"
github-repo: "lwvmobile/dsd-fme"
github-stars: 402
github-pushed: "2026-11-07"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 86
gh-activity-checked: "2026-11-09"
---

# dsd-fme

> Actively developed Digital Speech Decoder fork (Florida Man Edition) that decodes P25, DMR, NXDN, D-STAR, YSF, M17, ProVoice, dPMR and EDACS voice from SDR audio, with trunking and an NCurses UI.

## What it's for

DSD-FME decodes digital-voice protocols from radio audio and synthesizes the speech, built on the classic DSD codebase with heavy additions: an NCurses terminal UI, PulseAudio input/output, TCP direct-link audio from SDR++, built-in RTL-SDR tuning, RIGCTL radio control, trunk-tracking (P25 Phase 1/2, DMR Cap+/Con+/TIII, NXDN, EDACS, Hytera XPT), a conventional frequency scanner, LRRP/GPS position mapping, and per-call WAV recording. Input comes from a sound card, a wav recording, an SDR++ TCP sink, or an RTL dongle directly; audio can also be blasted to a remote UDP listener.

Auto-detection covers DMR BS/MS and P25; the remaining modes need an explicit `-f` switch (`-fi` NXDN48, `-fn` NXDN96, `-fp` ProVoice, `-fm` dPMR, `-fh`/`-fE` EDACS, `-fy` YSF, `-fz` M17, `-ft` stereo TDMA classes). It is Linux-desktop-focused and under active development — the lean, stable classic decoder lives on as the separate `dsd` tool.

## Typical use cases

### Live decode with the NCurses UI

Monitor a discriminator-tap or SDR audio stream with the terminal interface.

```
dsd-fme -N 2> voice.log
```

Outcome: opens the NCurses menu on PulseAudio input/output, auto-detects DMR and P25, and synthesizes speech to the speakers; console event output is redirected to voice.log, so run `tail -n 40 -f voice.log` in a second terminal to watch events. Keyboard shortcuts (see upstream examples doc) toggle synthesis, per-call WAV recording, trunking and more.

### Decode a saved recording

Work from a wav file instead of live audio.

```
dsd-fme -i recording.wav
```

Outcome: decodes a 48 kHz mono wav (as produced by SDR++ or GQRX) instead of live input; for 96 kHz DSDPlus raw-signal recordings, append `-s 96000` at the end of the command line.

### Decode straight from an RTL-SDR dongle

Let the decoder tune the dongle itself.

```
dsd-fme -fs -i rtl:0:446.0M:26:-2:12:0:2
```

Outcome: opens RTL device 0 tuned to 446.0 MHz with gain 26, -2 ppm error, 12 kHz bandwidth, squelch 0 and volume multiplier 2, and decodes DMR stereo (`-fs`; add `-xr` if the DMR signal is inverted). Every field after `rtl` is optional — plain `-i rtl` uses defaults.

### Track a trunked system fed by SDR++

Follow voice-channel grants while SDR++ supplies audio and tuning.

```
dsd-fme -fs -i tcp -T -U 4532 -C channel_map.csv -N 2> log.ans
```

Outcome: takes audio from the SDR++ TCP direct link (default localhost:7355), follows DMR trunking channel grants mapped through channel_map.csv, and retunes SDR++ over its RIGCTL port 4532; calls, talkgroups and events appear in the NCurses UI and log.ans. The same pattern works for P25 (drop `-fs`), NXDN (`-fi`), and EDACS (`-fE`).

### Stream decoded audio to a remote host

Send synthesis output over the network instead of to local speakers.

```
dsd-fme -fs -o udp:198.51.100.10:23470
```

Outcome: sends 8 kHz stereo signed-16 audio as UDP packets to 198.51.100.10; on the receiving host, play it with `socat stdio udp-listen:23470 | play --buffer 640 -q -b 16 -r 8000 -c2 -t s16 -`. Start the receiver after starting dsd-fme, and restart it whenever the decoder restarts.

*No corpus evidence — derived from upstream documentation.*
