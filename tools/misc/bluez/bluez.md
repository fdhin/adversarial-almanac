---
name: "bluez"
categories:
  - bluetooth
version: "5.87-2"
homepage: "http://www.bluez.org/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-07-04"
build-system: autotools
license: GPL-2.0-only
meta-checked: "2026-11-09"
github-repo: "bluez/bluez"
github-stars: 1177
github-pushed: "2026-11-07"
github-archived: false
github-issues: 130
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1019
gh-activity-checked: "2026-11-09"
---

# bluez

> The official Linux Bluetooth stack: bluetoothd daemon plus the bluetoothctl/btmon userland.

*No corpus evidence — derived from upstream documentation.*

## What it's for

BlueZ is the Bluetooth protocol stack for Linux: the `bluetoothd` D-Bus daemon that owns controllers and profiles, and the command-line utilities packaged with it. `bluetoothctl` is the current, supported control tool for both Classic (BR/EDR) and Low Energy — adapters, discovery, pairing, GATT — and `btmon` traces HCI traffic between kernel and controller; `btattach`, `l2ping` and friends round out the toolkit. The BlueZ 4-era utilities still cited by older guides — `hciconfig`, `hcitool`, `hciattach`, `rfcomm`, `sdptool`, `ciptool` — are deprecated and unmaintained: upstream builds them only with `--enable-deprecated`, Arch ships them in a separate `bluez-deprecated-tools` package, and some distros omit them entirely. Prefer `bluetoothctl` for anything new; the legacy tools are documented here because they still appear everywhere.

## Typical use cases

### Power on and scan from the shell

```
bluetoothctl <<EOF
power on
scan on
EOF
```

Outcome: enables the default adapter and starts discovery; discovered Classic and LE devices stream by with address, name and signal info. End with `scan off` (or Ctrl-D). The same heredoc form scripts any bluetoothctl command non-interactively.

### Inspect the local controller

```
bluetoothctl list
bluetoothctl show
```

Outcome: `list` enumerates adapters with their addresses; `show` prints the default adapter's properties — address, name, class, powered/pairable/discoverable state, UUIDs — the quick check that bluetoothd sees the hardware.

### Pair and connect to a device

```
bluetoothctl <<EOF
agent on
default-agent
pair 00:11:22:33:44:55
trust 00:11:22:33:44:55
connect 00:11:22:33:44:55
EOF
```

Outcome: registers a pairing agent, pairs with the device (PIN/passkey prompts appear in the terminal), marks it trusted so it reconnects automatically, and opens the connection.

### Trace HCI traffic

```
sudo btmon
```

Outcome: decodes every HCI command, event and ACL/LE frame exchanged with the controller in real time — the packet-level view when pairing, advertising or GATT transactions misbehave.

### Ping a Bluetooth device

```
sudo l2ping 00:11:22:33:44:55
```

Outcome: sends L2CAP echo requests to the device and prints per-packet round-trip times — a link-layer reachability and latency check that works without any profile connected. Stop with Ctrl-C.

### Legacy adapter survey with deprecated tools

```
hciconfig -a
hcitool scan
```

Outcome: the old-style pair — a full adapter state dump (address, features, packet types) followed by a Classic inquiry listing visible devices. Works only where the deprecated tools are packaged (`bluez-deprecated-tools` on Arch, the main bluez package on Kali); their interface is frozen, so treat them as read-only diagnostics and translate anything scripted into bluetoothctl.
