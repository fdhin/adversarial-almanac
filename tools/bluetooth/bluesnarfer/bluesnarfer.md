---
name: "bluesnarfer"
categories:
  - blackarch-bluetooth
version: "0.1"
homepage: "http://www.alighieri.org/project.html"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2018-11-12"
build-system: make
license: custom
meta-checked: "2026-11-09"
github-repo: "kimbo/bluesnarfer"
github-stars: 256
github-pushed: "2024-01-08"
github-archived: false
github-issues: 4
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bluesnarfer

> Bluesnarfing client that pulls phonebook data off a Bluetooth device over an RFCOMM channel using GSM AT commands.

## What it's for

bluesnarfer exploits the classic bluesnarfing exposure on older handsets: it opens an RFCOMM connection to a target device and issues GSM-extension AT commands to read, search, or delete phonebook entries, query device info, or send arbitrary AT commands. Reach for it when auditing legacy phones and embedded devices for unauthorized AT-level data access over Bluetooth. It does not crack PINs or pair — the target must be reachable and expose an AT-capable RFCOMM channel (channel 17 by default, overridable with `-C`; find working channels with `sdptool browse`). Modern phones generally prompt for permission or refuse the connection, so this is primarily a check against old stacks.

## Typical use cases

### Show device info for a target

Confirm the AT channel opens and the device answers basic queries.

```
bluesnarfer -b 00:11:22:33:44:55 -i
```

Outcome: prints the device information returned by the target's AT interface; if the connection fails, browse the target's services to find the AT channel and retry with `-C <chan>`.

### Dump a range of phonebook entries

Read entries out of the target's phonebook.

```
bluesnarfer -r 1-100 -b 00:11:22:33:44:55
```

Outcome: prints phonebook entries 1 through 100 from the default memory store over the RFCOMM link.

### List phonebook stores, then dump one

Enumerate which memory stores the device exposes before selecting one.

```
bluesnarfer -l -b 00:11:22:33:44:55
bluesnarfer -s SM -r 1-50 -b 00:11:22:33:44:55
```

Outcome: the first command lists store codes (e.g. `SM` for the SIM phonebook, `DC` dialed calls, `RC` received calls); the second selects the SIM store and dumps entries 1-50 from it.

### Send a custom AT command

Probe an AT command the built-in modes do not cover.

```
bluesnarfer -c AT+CGMI -b 00:11:22:33:44:55
```

Outcome: prints the raw AT response from the device — useful for manufacturer identification and command-support probing.

*No corpus evidence — derived from upstream documentation.*
