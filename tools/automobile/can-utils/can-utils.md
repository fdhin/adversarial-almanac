---
name: "can-utils"
categories:
  - blackarch-automobile
version: "1304.834ceef"
homepage: "https://github.com/linux-can/can-utils"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: cmake
license: GPL2
meta-checked: "2026-11-09"
github-repo: "linux-can/can-utils"
github-stars: 2955
github-pushed: "2026-10-14"
github-archived: false
github-issues: 61
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 52
gh-activity-checked: "2026-11-09"
---

# can-utils

> The canonical Linux SocketCAN userspace suite — `candump`, `cansend`, `cangen`, `canplayer` and ISO-TP/J1939 helpers for capturing, crafting, replaying and routing CAN bus traffic from the command line.

## What it's for

Working with Controller Area Network buses on Linux through the kernel's SocketCAN layer, where a CAN adapter (or the virtual `vcan0` interface for practice) appears as a normal network interface. The suite covers the whole workflow of vehicle and embedded CAN analysis: passively dumping traffic, filtering by CAN ID, logging to compact logfiles, injecting single frames, generating load, replaying captures, sniffing ISO-TP (ISO 15765-2) transport traffic that carries diagnostics such as UDS, and routing frames between buses with an in-kernel gateway. Every tool is a small filter-style CLI meant to be combined in pipelines. It does not include a GUI and it does not speak to vehicles by itself — an interface must already exist (created with `ip` from iproute2, or `slcand` for serial adapters). Interfaces are usually brought up first with `ip link set can0 type can bitrate 500000 && ip link set up can0`.

## Typical use cases

### Watch all traffic on a CAN interface

Live view of every frame passing a bus.

```
candump can0
```

Outcome: prints one line per received frame — interface, hex CAN ID, `#`, hex data bytes — plus a running frame counter, until interrupted with Ctrl-C. `candump any` receives from every CAN interface at once.

### Capture only frames matching specific CAN IDs

Narrow a noisy bus down to the IDs of interest.

```
candump can0,123:7FF,400:700
```

Outcome: only frames whose ID matches `can_id:can_mask` pairs are shown (a frame matches when `received_id & mask == can_id & mask`), here exact ID 123 and the 400–4FF range. Use `~` instead of `:` to invert a filter.

### Log traffic to a compact logfile

Record a session for later replay or offline analysis.

```
candump -l can0
```

Outcome: writes frames in the compact one-line-per-frame format to `candump-YYYY-MM-DD_HHMMSS.log` in the current directory (screen output is silenced). This is the file format `canplayer` and `log2asc` consume.

### Inject a single frame

Put one crafted frame on the bus.

```
cansend can0 123#DEADBEEF
```

Outcome: one data frame with ID 123 and payload `DEADBEEF` is transmitted on can0. The same syntax family covers remote frames (`123#R8`) and CAN FD frames (`123##111.2233` — `##` plus a flags nibble), and IDs/data are always hex.

### Generate continuous or crafted traffic

Create load or a stream of controlled frames for testing.

```
cangen can0 -g 4 -I 42A -L 1 -D i -v -v
```

Outcome: emits frames every 4 ms with fixed ID 42A, length 1 and incrementing payload (`-D i`), printing each sent frame twice-verbosely. Without `-I/-L/-D` the values are random; `-n 25` stops after 25 frames; `-g 0` floods the bus.

### Replay a captured logfile

Send a previously recorded session back onto a bus.

```
canplayer -I candump-2026-11-08_134522.log
```

Outcome: frames are retransmitted onto the interfaces named in the logfile, preserving the original timing. An assignment like `vcan0=can0` redirects frames logged from can0 onto vcan0 instead; `-t` ignores timestamps and sends immediately.

### Spot which bytes change per ID

Diff-style view that highlights payload changes per CAN ID.

```
cansniffer can0
```

Outcome: a full-screen curses display listing one row per active CAN ID where unchanged data bytes dim and changed bytes light up — the quickest way to correlate a physical action (door lock, indicator) with the bytes that encode it.

### Measure bus utilization

Quantify how loaded a bus is.

```
canbusload can0@500000
```

Outcome: continuously recalculates and prints the busload percentage for can0 assuming the 500 kbit/s bitrate given after `@` (a data bitrate for CAN FD may follow after a comma), updating the same terminal line; add `-b` for a bargraph, `-r` for top-style redraw.

### Decode diagnostics riding on ISO-TP

Read UDS/diagnostic messages hidden inside multi-frame ISO-TP transfers.

```
isotpdump -s 7e0 -d 7e8 -u can0
```

Outcome: reassembles ISO 15765-2 PDUs exchanged between tester ID 7e0 and ECU ID 7e8 in both directions and, with `-u`, annotates each message as a UDS service request `[SRQ]`, positive response `[PSR]` or negative response `[NRC]` — visible diagnostic traffic without talking to the ECU.

### Route frames between two buses

Bridge or gate traffic from one interface to another in-kernel.

```
cangw -A -s can0 -d vcan0 -e
```

Outcome: registers a CAN gateway rule so frames received on can0 are forwarded to vcan0 (frames echoed on vcan0 as well with `-e`, required on virtual interfaces); `cangw -L` lists rules and `cangw -F` flushes them. Optional `-f <can_id>:<mask>` filters what is forwarded.

*No corpus evidence — derived from upstream documentation.*
