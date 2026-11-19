---
name: "gr-gsm"
categories:
  - blackarch-radio
version: "1191.71e9630"
homepage: "https://github.com/ptrkrysik/gr-gsm"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-15"
build-system: cmake
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "ptrkrysik/gr-gsm"
github-stars: 1510
github-pushed: "2026-10-19"
github-archived: false
github-issues: 162
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gr-gsm

> GNU Radio blocks and companion applications for receiving and decoding GSM transmissions.

## What it's for

Receiving GSM downlink signals with an SDR (RTL-SDR, HackRF, BladeRF, USRP and anything supported by gr-osmosdr) and turning them into decodable output: base-station listings, GSMTAP streams for Wireshark, captured IQ files, decoded signalling channels and even voice when the ciphering key Kc is known. It descends from the Airprobe gsm-receiver and is the standard building block for GSM monitoring setups. It receives and decodes — it is not a fake base station by itself; the bundled `grgsm_trx` instead implements a software transceiver that pairs with OsmocomBB's trxcon to act as a mobile station. Encrypted traffic stays encrypted unless you supply the session key.

It ships a set of installed command-line applications: `grgsm_scanner`, `grgsm_livemon` / `grgsm_livemon_headless`, `grgsm_capture`, `grgsm_channelize`, `grgsm_decode` and `grgsm_trx`, plus the underlying GNU Radio blocks for building custom flow graphs in GNU Radio Companion.

## Typical use cases

### Scan a GSM band for base stations

Find which ARFCNs (channels) carry downlink carriers in your area before monitoring anything.

```
grgsm_scanner -b GSM900
```

Outcome: sweeps the GSM900 band and prints one line per detected BTS with ARFCN, frequency, cell parameters and signal power, sorted by power. Use the strongest ARFCN's downlink frequency as the `-f` value for the monitoring tools below. Other bands (P-GSM, DCS1800, PCS1900) work via `-b`; `-p` corrects a known PPM offset of the SDR clock.

### Live-monitor one C0 channel into Wireshark

Tune to a downlink frequency and stream the decoded GSM layer 2/3 as GSMTAP packets.

```
grgsm_livemon -f 941.8M -g 40
```

Outcome: a QT GUI opens with spectrum and waterfall plus sliders for frequency, gain and PPM; decoded bursts are sent as UDP GSMTAP to the loopback interface. Analyse them in Wireshark with `wireshark -k -f udp -Y gsmtap -i lo` to read SYSTEM INFORMATION, paging and SMS/SDCCH messages of that cell. The headless twin `grgsm_livemon_headless` takes the same flags and runs without a GUI.

### Capture GSM IQ samples to a file

Record a fixed-length burst of raw signal for off-line decoding.

```
grgsm_capture -f 941.8e6 -g 40 -s 2000000 -T 20 gsm_capture.cfile
```

Outcome: records 20 seconds of complex float IQ at the given frequency, sample rate and gain into `gsm_capture.cfile`, printing progress until the capture completes. The file feeds `grgsm_decode`; `-T` is the recording length in seconds and `-a ARFCN` can replace `-f` if you prefer channel numbers.

### Decode a capture's signalling or voice channel

Run the off-line decoder against the captured file.

```
grgsm_decode -c gsm_capture.cfile -f 941.8e6 -s 2000000 -m BCCH -t 0
```

Outcome: decodes timeslot 0 as a BCCH/CCCH channel and prints the decoded messages; use `-m SDCCH8` or `-m TCHF` (with `-t` timeslot and `-d` speech codec) for dedicated and traffic channels. With a known session key, `-e 1 -k 0x1111111111111111` decrypts A5/1-protected traffic, and for TCH the speech frames are written to the output file given by `-o` (default `/tmp/speech.au.gsm`).

### Split a wideband capture into single channels

Turn one wide SDR recording into per-ARFCN files.

```
grgsm_channelize -i wideband.cfile -f 925.2e6 990 991 992 993 994 995
```

Outcome: resamples the wideband capture and writes one file per listed ARFCN into a destination directory (named after the input file unless `-d` is given), each at 1 Msps by default, ready to be decoded individually by `grgsm_decode` — useful when a single 2 Msps+ capture covers several cells.

*No corpus evidence — derived from upstream documentation.*
