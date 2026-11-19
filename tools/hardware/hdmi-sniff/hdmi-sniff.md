---
name: "hdmi-sniff"
categories:
  - blackarch-hardware
version: "5.f7fbc0e"
homepage: "https://github.com/ApertureLabsLtd/hdmi-sniff"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "ApertureLabsLtd/hdmi-sniff"
github-stars: 97
github-pushed: "2014-08-27"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hdmi-sniff

> Sniffs the HDMI DDC (I2C) channel between a source and a display via a Bus Pirate — written to demonstrate how easily HDCP keys can be recovered off the wire.

## What it's for

Capturing traffic on the Display Data Channel — the I2C bus an HDMI source and sink use to exchange EDID data and negotiate HDCP keys. Wired across an HDMI breakout cable into a Bus Pirate (or GPHHT) on a serial port, it puts the adapter into I2C sniffer mode and prints the DDC exchanges live; with Rich Wareham's `hdcp-genkey` (`generate_key.py`) placed next to the scripts, it decodes captured KSVs/HDCP exchanges into usable key material. It exists as a proof-of-concept for the 2013 HDCP-breaking work, and could be extended to decode other DDC traffic (EDID, MCCS). It is a quick 2013-era Python 2 hack, not a maintained analyzer: no GUI, no PCAP output, and it depends on external hardware (breakout cable + Bus Pirate). This is passive interception of a display connection for research into HDCP/EDID — attacking content protection you don't own is not a legitimate use.

## Typical use cases

### Sniff live DDC traffic between a source and a display

Wire the breakout cable's DDC SDA/SCL lines to the Bus Pirate, connect it to the host, then:

```
hdmi-sniff /dev/ttyUSB0
```

Outcome: the script opens the serial port at 115200 baud, detects the Bus Pirate (falling back to GPHHT mode otherwise), switches it into I2C sniffer mode, and starts printing every byte seen on the DDC bus — plug/unplug or re-handshake the HDMI link and the EDID reads and HDCP exchanges scroll by. Run the raw script directly with `python2 hdmi-sniff.py /dev/ttyUSB0` if it is not packaged.

### Recover HDCP key material from a captured handshake

With `generate_key.py` from hdcp-genkey placed in the same directory:

```
hdmi-sniff /dev/ttyUSB0
```

Outcome: while the DDC traffic streams, the hdmi_ddc layer decodes HDCP messages found on the bus and, via hdcp-genkey, converts the sniffed KSV exchange into sink/source key data in human-readable form — reproducing the demonstrated recovery of HDCP crypto keys from a live HDMI connection.

*No corpus evidence — derived from upstream documentation.*
