---
name: "caeruleus"
categories:
  - bluetooth
homepage: "https://github.com/praetorian-inc/caeruleus"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: go
license: Apache-2.0
meta-checked: "2026-11-13"
github-repo: "praetorian-inc/caeruleus"
github-match: "exact"
github-stars: 60
gh-checked: "2026-11-13"
worth: 5.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# caeruleus

> Single Go binary for the whole BLE assessment workflow on Linux/BlueZ — scan, GATT read/write, notifications, fuzzing and structured security checks with JSON output.

## What it's for

Caeruleus (Praetorian) consolidates Bluetooth Low Energy security testing
into one command surface over the Linux BlueZ stack (D-Bus plus raw
L2CAP/HCI sockets), replacing the usual pile of deprecated `gatttool`/
`hcitool`, `bettercap ble.recon`, `bluetoothctl` and one-off Bleak
scripts. It covers the full lifecycle: discover devices, browse and
read/write the whole GATT tree, capture notifications, fuzz writable
characteristics, and run repeatable assessment probes (unauthenticated
access, encryption enforcement, pairing downgrades, write-without-response
abuse, unauthenticated DFU). Every command emits `-o json`/`jsonl` so
scripts and LLM agents can consume results without scraping tables. It is
Linux-only by design and needs a real BLE adapter; some raw-socket
commands need root. It does not do Bluetooth Classic (BR/EDR) auditing or
sniffing off-air traffic — for that you still need an SDR/Ubertooth-class
setup.

*No corpus evidence — derived from upstream documentation.

## Typical use cases

### Check the adapter, then discover nearby devices

Sanity-check BlueZ and scan for peripherals.

```
caeruleus doctor && caeruleus scan
```

Outcome: `doctor` prints an OK/WARN/FAIL checklist of the BLE stack
(bluetoothd, adapter power, live scan probe, MTU) with a concrete fix
command per non-OK finding; `scan` lists advertising devices with
address, type, RSSI and inferred device type (add `--live` for streaming
`jsonl` events).

### Dump the whole GATT tree with values

Enumerate services, characteristics and current values in one shot.

```
caeruleus enumerate -b 9C:9C:1F:F2:88:86 --values
```

Outcome: prints every service/characteristic handle pair with UUID,
properties and current value in hex+ASCII — flag-looking strings (tokens,
firmware identifiers) jump out immediately; `--compact` emits one
`key=value` line per characteristic for grep or an agent context window,
and `--raw` (root) reads the database straight over ATT on old BlueZ.

### Read and write a single handle

Poke individual characteristics like gatttool used to.

```
caeruleus read -b 9C:9C:1F:F2:88:86 -a 0x002d
caeruleus write -b 9C:9C:1F:F2:88:86 -a 0x002f -s "praetorian" --req
```

Outcome: `read` prints the handle's value in hex and ASCII; `write` sends
the string as a write request (`--req`) and reports bytes written —
handles accept hex (`-s deadbeef`) or plain strings, and `raw-write`
takes arbitrary payload bytes.

### Capture notifications from a characteristic

Listen for indicications/notifications (e.g. a sensor's live feed).

```
caeruleus listen -b 9C:9C:1F:F2:88:86 -a 0x0035 -o jsonl
```

Outcome: subscribes to the handle's notifications and streams one JSON
record per event until interrupted; `--trigger-value <hex>` atomically
performs a write then captures the response — the pattern for
command/response protocols over GATT.

### Run the security assessment probes

Audit exposure and pairing weaknesses as structured findings.

```
caeruleus recon -b 9C:9C:1F:F2:88:86 -o json
caeruleus assess check-auth -b 9C:9C:1F:F2:88:86 --probe-write
```

Outcome: `recon` fingerprints the device and audits its GATT tree; each
`assess` subcommand (`check-auth`, `encryption`, `pairing`, `wwr`, `dfu`)
probes one weakness class and emits `{address, test, summary, findings[]}`
with severity/handle/UUID/evidence — secret-looking values are
cross-checked against the Titus rule set and unpaired-readable keys are
escalated automatically.

### Fuzz a writable characteristic

Throw malformed writes at a target handle.

```
sudo caeruleus fuzz write -b 9C:9C:1F:F2:88:86 -a 0x002f
```

Outcome: generates fuzzed writes against the characteristic (root needed
for `--raw` L2CAP framing) and reports the device's responses/reset
behavior; `fuzz replay` re-sends captured traffic.

### Hold one connection for scripted work

Avoid the ~1.5s reconnect cost per command.

```
caeruleus serve -b 9C:9C:1F:F2:88:86 &
caeruleus send read -a 0x002d
caeruleus shell -b 9C:9C:1F:F2:88:86
```

Outcome: `serve` keeps a warm GATT link open over a Unix socket that
`send` reuses instantly; `shell` is an interactive gatttool-style session
and `batch` replays commands from stdin over one persistent connection —
`caeruleus recipes` lists 19 keyword-searchable ready-made workflows.
