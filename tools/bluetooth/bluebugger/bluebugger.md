---
name: "bluebugger"
categories:
  - blackarch-bluetooth
version: "0.1"
homepage: "http://packetstormsecurity.com/files/54024/bluebugger.1.tar.gz.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "webdragon63/Bluebugger"
github-stars: 32
github-pushed: "2025-06-19"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bluebugger

> BlueBug attack client: opens an unauthorized RFCOMM channel to a vulnerable handset and drives AT commands to dump its info, phonebook and SMS.

*No corpus evidence — derived from upstream documentation (the packaged upstream source tarball, bluebugger 0.1, is the reference).*

## What it's for

BlueBug (found by Martin Herfurt, 2004) affected 2000s-era handsets — chiefly the Nokia 6310i — whose serial-over-Bluetooth service accepted AT commands on a fixed RFCOMM channel without pairing or authorization. bluebugger is a small C implementation of that technique: given a target BD_ADDR it binds an rfcomm device, connects on the vulnerable channel and replays AT sequences that read device info, phonebook entries and SMS messages, dial a number, or run any custom AT command. It needs root and the bluez stack, and it does not discover targets — you must already know the address (use `hcitool scan` first). It attacks the AT channel only; pulling files over OBEX is the bluesnarf family's territory (see [[bluesnarfer]]). In practice it is a lab/audit tool for verifying that legacy handsets still expose the vulnerable channel.

## Typical use cases

### Dump info, phonebook and messages from a vulnerable handset

```
sudo bluebugger -a AA:BB:CC:DD:EE:FF
```

Outcome: bluebugger resolves the target's name, binds an rfcomm device and connects on channel 17 (the classic BlueBug channel); with no mode words given it runs the three default modes — `info`, `phonebook`, `messages` — printing the AT responses to the terminal. Empty or garbled responses mean the handset is not BlueBug-vulnerable on that channel.

### Run custom AT commands and save the session

```
sudo bluebugger -a AA:BB:CC:DD:EE:FF -o bluebug-session.txt +GMI +GMM
```

Outcome: connects as above but runs only the given commands — `+GMI` and `+GMM` query manufacturer and model identification — and writes everything to `bluebug-session.txt` instead of stdout. Modes combine freely on the command line (e.g. `info phonebook +GMI`); `-c <channel>` selects a different RFCOMM channel and `-n` skips the remote-name lookup for faster runs.
