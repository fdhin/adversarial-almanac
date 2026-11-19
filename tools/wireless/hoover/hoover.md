---
name: "hoover"
categories:
  - blackarch-wireless
version: "4.9bda860"
homepage: "https://github.com/xme/hoover/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "xme/hoover"
github-stars: 189
github-pushed: "2022-08-31"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hoover

> Perl sniffer that watches 802.11 probe requests and prints which SSIDs nearby devices are searching for.

## What it's for

hoover.pl (by Xavier Mertens, original idea by David Nelissen) is a small probe-request collector: it puts a wireless interface to work listening for the probe requests that phones and laptops broadcast while hunting for their remembered networks, and prints each observed SSID — revealing the preferred-networks list (and often home or hotel names) of every device in radio range, without connecting to anything. It is client-side wireless reconnaissance for situational awareness and social-engineering prep, not an attack tool: it only listens. Under the hood it drives iwconfig/ifconfig and tshark, and it can keep a flat-file log of the SSID/MAC pairs it has seen.

## Typical use cases

### Sniff probe requests live

```
sudo hoover.pl --interface=wlan0
```

Outcome: the interface is used to capture probe requests and every SSID that nearby devices probe for is printed as it arrives, giving a live feed of the preferred networks of passers-by.

### Verbose output

```
sudo hoover.pl --interface=wlan0 --verbose
```

Outcome: adds verbose detail to the console output (per-packet information beyond the condensed default line), useful when you want to see the underlying capture activity rather than just the distilled SSID stream.

### Log all observed SSIDs and MACs to a file

```
sudo hoover.pl --interface=wlan0 --dumpfile=probes.txt
```

Outcome: besides printing to the console, every SSID/MAC-address pair discovered is appended to the flat file `probes.txt`, building a cumulative record of devices and the networks they remember; paths to the iwconfig/ifconfig/tshark binaries can be overridden with `--iwconfig-path`, `--ifconfig-path` and `--tshark-path` when they are not in the default locations.

*No corpus evidence — derived from upstream documentation (options verified against the packaged source's usage text).*
