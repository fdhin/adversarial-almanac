---
tool: "caeruleus"
build-system: "go"
upstream: "https://github.com/praetorian-inc/caeruleus"
recipe: "upstream"
license: "Apache-2.0"
---

# Installing caeruleus

> Single Go binary for the whole BLE assessment workflow on Linux/BlueZ — scan, GATT read/write, notifications, fuzzing and structured security checks with JSON output.

Source: hacktricks feed; upstream [`praetorian-inc/caeruleus`](https://github.com/praetorian-inc/caeruleus).

## Requirements

Linux with BlueZ (`bluetoothd`) and a BLE adapter. Does not build or run
on macOS/Windows. Some raw-socket commands (`fuzz write --raw`,
`conn-params`, `monitor`, `raw-write`, `mtu --set`, `bdaddr --set`) need
root.

## Install with Go

```bash
go install github.com/praetorian-inc/caeruleus/cmd/caeruleus@latest
```

## Prebuilt binaries

x86_64 and arm64 archives on the
[releases page](https://github.com/praetorian-inc/caeruleus/releases) —
extract and put `caeruleus` on `$PATH`.

## From source

```bash
git clone https://github.com/praetorian-inc/caeruleus
cd caeruleus
make build        # -> ./caeruleus
```

## Not yet packaged

No distro packages this tool (checked 2026-11-13).

Backlinks: [[bluetooth]] · [[caeruleus]]
