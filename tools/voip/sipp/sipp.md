---
name: "sipp"
categories:
  - blackarch-voip
version: "1574.5c4e8dc"
homepage: "http://sipp.sourceforge.net/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2025-12-27"
build-system: cmake
license: GPL
meta-checked: "2026-11-09"
github-repo: "SIPp/sipp"
github-stars: 1156
github-pushed: "2026-11-02"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 600
gh-activity-checked: "2026-11-09"
---

# sipp

> The reference open-source SIP traffic generator — drives UAC/UAS call flows from XML scenarios with rate control, media handling and live statistics.

## What it's for

Load-testing and functional testing of SIP servers, proxies and PBXes. SIPp (GPL, C++, maintained in the SIPp/sipp organization repo; docs at sipp3.readthedocs.io) ships embedded SipStone scenarios — UAC (the default), UAS, regexp, branching client/server and 3PCC variants — runnable with no preparation via `-sn`, and dumps any of them as documented XML (`-sd`) to use as a template for custom scenarios (`-sf`), where the message flow, retransmission timers, authentication, conditional branching, Lua hooks and per-call values injected from CSV files (`-inf`) are all scripted. Rate is governed by `-r` (calls per second), `-rp` (rate period), `-l` (simultaneous-call limit), `-m` (stop after N calls) and `-users` (constant-load mode), adjustable live from the keyboard (`+`, `-`, `*`, `/`). Transports cover UDP, TCP, TLS, WebSocket/WSS and SCTP; media support includes RTP echo, RTP streaming and PCAP playback; statistics screens, message/RTT tracing (`-trace_msg`, `-trace_rtt`) and background mode (`-bg`) round out long soak tests. It is a test instrument, not an attack tool — its security use is measuring how a lab PBX behaves under call load.

## Typical use cases

### Generate INVITE load with the default UAC scenario

```
sipp -sn uac 198.51.100.10
```

Outcome: runs the embedded SipStone UAC scenario (INVITE, ACK, BYE) against the target at the default rate of 10 calls/s; the console screen shows live counters for started/succeeded/failed calls and the distribution of responses; press `q` to quit, `+`/`*` to raise the rate.

### Run the answering side

```
sipp -sn uas
```

Outcome: binds locally and acts as a minimal UAS — answers INVITEs with provisional and 200 responses and handles ACK/BYE — so a proxy between the two SIPp instances can be exercised end-to-end.

### Bound a load profile

```
sipp -sn uac -r 50 -rp 1000 -l 500 -m 10000 198.51.100.10
```

Outcome: 50 calls every 1000 ms, at most 500 simultaneous calls, and a clean exit after 10000 calls — a repeatable capacity test with defined stopping points instead of an open-ended flood.

### Turn a built-in scenario into an editable XML file

```
sipp -sd uac > uac.xml
sipp -sf uac.xml -i 198.51.100.5 -s 1000 198.51.100.10
```

Outcome: `-sd uac` prints the embedded scenario XML (complete with inline syntax documentation) to `uac.xml`; after editing it, `-sf` runs the customized flow — `-s 1000` sets the request-URI user (default `service`) and `-i` the local address used in Via/From/Contact. Other dumpable scenarios include `uas`, `regexp`, `branchc`, `branchs` and the 3PCC variants.

### Drive per-call values from a CSV file

```
sipp -sf register.xml -inf users.csv -m 1000 198.51.100.10
```

Outcome: `users.csv` (whose first line declares SEQUENTIAL, RANDOM or USER ordering) supplies `[field0]`, `[field1]`, ... values substituted per call in the scenario — distinct usernames, passwords or called numbers for a registration or dialing test — and the run stops after 1000 calls.

### Exercise the media path with RTP echo

```
sipp -sn uac -rtp_echo -mi 198.51.100.5 198.51.100.10
```

Outcome: RTP/UDP packets arriving on the media port are echoed back to their sender, verifying the target's media forwarding end to end; `-mi` pins the local media IP. Combine with `-t t1` (TCP one socket) or `-bg` (detach) as the test requires.

*No corpus evidence — derived from upstream documentation.*
