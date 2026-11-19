---
name: "lte-cell-scanner"
categories:
  - blackarch-radio
version: "26.e7f71cb"
homepage: "https://github.com/JiaoXianjun/LTE-Cell-Scanner"
packaged-by: [blackarch]
blackarch-builddate: "2024-10-08"
build-system: cmake
license: GPL
meta-checked: "2026-11-09"
github-repo: "JiaoXianjun/LTE-Cell-Scanner"
github-stars: 806
github-pushed: "2024-01-26"
github-archived: false
github-issues: 21
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# lte-cell-scanner

> Enhanced OpenCL-accelerated TDD/FDD LTE cell scanner and tracker for HackRF, RTL-SDR and BladeRF.

## What it's for

Finding LTE cells blind: it searches a frequency for LTE downlink carriers and reports each detected cell's physical ID, duplexing (TDD/FDD), antenna configuration, bandwidth in resource blocks, receive power and residual frequency offset, using PSS/SSS correlation that OpenCL can offload to a GPU (it also builds without OpenCL). The same repo ships a tracking/decoding receiver (`LTE-Tracker`) that locks to a found cell and decodes downlink channels, plus full Matlab/Octave scripts (`LTE_DL_receiver`) that carry a capture from IQ samples all the way to PDSCH output and decoded RRC SIB messages. Hardware support is HackRF, RTL-SDR and BladeRF; USRP works via the Matlab/Octave path only. It is a receiver-side measurement and research tool — it does not transmit, and it will not decode user-plane content of encrypted bearers; it stops at what the cell broadcasts.

The installed command surface is the two binaries `CellSearch` and `LTE-Tracker`; `-h` on either lists every argument.

## Typical use cases

### Search a frequency for LTE cells

Point it at a suspected downlink frequency and see what is really there.

```
CellSearch --freq-start 1890000000
```

Outcome: after a short correlation pass it prints per-cell detection lines and a summary table — duplex mode, cell ID, centre frequency, frequency offset, RX power, CP type, resource-block count and the crystal-correction factor for your SDR — e.g. `TDD 253 2 1890M -48h -17 N 100 ...`. Feed the reported cell/frequency to `LTE-Tracker` or your phone's engineering display to confirm. If the reported input level averages far below 1, add receive gain.

### Sweep a weak cell with retries and gain

Improve detection odds on marginal signals.

```
CellSearch --freq-start 1890000000 -g 60 --num-try 3
```

Outcome: performs three independent search passes at the frequency with the SDR gain set to 60 (the `-g` hardware gain, used instead of the default), printing any cell found on any attempt — weak cells that a single pass misses get several chances. `-t` additionally disables twisted carrier/sampling-clock mode when an external mixer or converter feeds the SDR.

### Record and replay an IQ capture through the search

Save the raw signal while searching, then re-run detection off-line.

```
CellSearch --freq-start 1860e6 --recbin capture.bin
```

Outcome: searches 1.86 GHz while simultaneously writing the received IQ samples to `capture.bin`; later, `CellSearch --loadbin capture.bin` re-runs the identical detection on the file with no SDR attached — the way to iterate on parameters against a fixed capture.

### Track and decode a found cell

Lock onto a detected carrier and follow its transmissions.

```
LTE-Tracker -f 1890000000
```

Outcome: synchronizes to the cell, tracks frequency drift, and prints per-subframe decode status — system frame number, TDD up/downlink configuration, PHICH/PDCCH activity and RNTIs — streaming as long as the signal stays locked. The companion Matlab/Octave `LTE_DL_receiver(1890)` takes the same capture further, down to asn1c-decoded SIB contents.

*No corpus evidence — derived from upstream documentation.*
