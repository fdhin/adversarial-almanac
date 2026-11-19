---
name: "hidattack"
categories:
  - blackarch-bluetooth
version: "0.1"
homepage: "http://mulliner.org/bluetooth/hidattack.php"
packaged-by: [blackarch]
blackarch-builddate: "2025-11-30"
build-system: make
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# hidattack

> Proof-of-concept that injects keystrokes into a Bluetooth HID host by replaying Linux input-event key presses over the HID profile.

## What it's for

Attacking the host side of the Bluetooth Human Interface Device architecture (Collin Mulliner, 2005): instead of pairing a physical keyboard, hidattack presents itself as (or connects to) a HID keyboard service and replays key-press events from a `.inp` file, so the victim's HID server delivers the scan codes as if they were typed. The event file uses the same format as `/dev/input/eventX`; the shipped `ha.inp` injects the four letters `0wnD`, and the source tree's `dump_event` helper generates that sample file. Client mode connects out to a target HID host; server mode waits for a host to connect. Classic Bluetooth only — nothing here applies to USB HID or Bluetooth Low Energy keyboards — and it is explicitly a demo of the attack class, not a polished injection framework.

## Typical use cases

### Inject keystrokes into a target HID host

```
sudo hidattack -e ha.inp -c AA:BB:CC:DD:EE:01
```

Outcome: connects to the HID host at the given address, replays the events in `ha.inp` (the sample types `0wnD` on the victim), and prints the event stream as it is sent. Replace or extend `ha.inp` with additional `/dev/input/eventX`-format records to type arbitrary sequences.

### Impersonate a keyboard and wait for the host

```
sudo hidattack -e ha.inp -s
```

Outcome: server mode — registers a keyboard with the local SDP server and waits for a Bluetooth HID host to connect to it, then replays `ha.inp` to whoever attaches. This covers scenarios where the target initiates the connection or pairing; `hidattack -h` prints the full flag summary (`-s` server, `-c <BD_ADDR>` client, `-e <FILE>` event file).

*No corpus evidence — derived from upstream documentation.*
