---
name: "hak5-wifi-coconut"
categories:
  - wireless
version: "1.1.0-0parrot1"
homepage: "https://hak5.org"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: cmake
meta-checked: "2026-11-09"
github-repo: "hak5/hak5-wifi-coconut"
github-stars: 156
github-pushed: "2024-02-25"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hak5-wifi-coconut

> Userspace port of the rt2800usb kernel driver that presents the Hak5 Wi-Fi Coconut's radios as one device capturing every 2.4 GHz channel at once.

## What it's for

The Hak5 Wi-Fi Coconut is a capture device containing fourteen RT2800USB-based radios — enough to monitor all 2.4 GHz channels simultaneously. This package is its software: the Linux kernel drivers for the radio rewritten as a normal userspace program (so it works identically on Linux, macOS and Windows without kernel modules), plus the `wifi_coconut` capture tool that unifies the radios into a single channel-aware monitor interface and writes standard pcap output. The device is receive-only — transmitting on Coconut radios can damage the hardware, so keep injecting tools away from it. For continuous monitoring, Kismet integrates it natively as the `coconut` data source.

## Typical use cases

### Interactive full-band capture

```
sudo wifi_coconut
```

Outcome: opens the interactive channel display, enables all detected radios and starts logging traffic from the entire 2.4 GHz band in one pass; LEDs on the device blink with traffic. Stop with Ctrl-C.

### Headless capture to a pcap file

```
sudo wifi_coconut --pcap capture.pcap --no-display
```

Outcome: skips the channel UI and writes all radios' traffic (radiotap-formatted, with signal and channel annotations) to `capture.pcap`; `--wait-for-coconut` makes it block until a device is plugged in, useful for scripted or startup captures, and `--plain-dot11` strips the radiotap headers if a tool wants plain 802.11 frames.

### Pipe live traffic into another tool

```
sudo wifi_coconut --pcap - --quiet | tshark -r - -Y eapol
```

Outcome: with `--pcap -` the pcap stream goes to stdout, so standard pcap consumers can process the full-band capture live — here tshark filters EAPOL handshakes as they arrive anywhere in the band.

### Enumerate connected Coconuts

```
wifi_coconut --list-coconuts
```

Outcome: lists the detected Wi-Fi Coconut devices (their coconut-N unit numbers) and exits; with several devices attached, pass `--coconut-device=N` to the capture run to select one.

### Use the Coconut as a Kismet data source

```
kismet -c coconut:name=WifiCoconut
```

Outcome: Kismet (with the `kismet-capture-hak5-wifi-coconut` capture helper installed, run as root) claims all the Coconut's radios as a single datasource and logs/detects across the whole band; on Linux the radios can alternatively be assigned individually and Kismet will distribute channels across them.

*No corpus evidence — derived from upstream documentation (flags verified against the packaged source's usage text; Kismet integration verified against the vendor docs).*
