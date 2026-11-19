---
name: "rtp-flood"
categories:
  - blackarch-voip
version: "1.0"
homepage: "http://www.hackingexposedvoip.com/"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-07"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# rtp-flood

> BlackArch's package name for Mark Collier and Mark O'Brien's rtpflood — canonical note: [[rtpflood]].

`rtp-flood` is the BlackArch package for the "RTP Flooder" tool that the Hacking Exposed VoIP companion site (hackingexposedvoip.com) distributed as `tools/rtpflood.tar.gz`, written by Mark D. Collier and Mark O'Brien (SecureLogix, 2006). It is not a separate tool from [[rtpflood]]: Kali packages the identical 2006 v1.0 source under the name `rtpflood`, taken from the authors' later Hacking VoIP site, and both build the same single-file `rtpflood.c` command-line flooder. The full reference with the verified command line and outcome lives in [[rtpflood]]; the sibling media-injection tools from the same authors are [[rtpinsertsound]] and [[rtpmixsound]].

*No corpus evidence — derived from upstream documentation.*
