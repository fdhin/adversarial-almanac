---
name: "gpshell"
categories:
  - hardware
version: "1.4.4"
homepage: "http://sourceforge.net/projects/globalplatform/"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike, aur]
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "kaoh/globalplatform"
github-stars: 99
github-pushed: "2026-10-05"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-09"
worth: 7.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 258
gh-activity-checked: "2026-11-09"
---

# gpshell

> GlobalPlatform command shell — a script interpreter for managing applications on smart cards over ISO-7816 / GlobalPlatform secure channels.

## What it's for

Talking to a GlobalPlatform card's card manager / security domain: it establishes SCP secure channels with card keys, uploads (loads), instantiates, deletes and lists applets, and manages card keys. It is the standard open-source tool for provisioning Java Card applets — install a CAP file onto a card, delete a stuck applet, or rotate card keys. Scripts are plain text files chaining shell-like commands (`establish_context`, `card_connect`, `open_sc`, `install`, ...) executed by the `gpshell` binary; it speaks PC/SC via the bundled connection plugin, so pcscd and a reader must be working first. Careless keying matters: cards usually lock themselves after around ten failed mutual authentications, and a locked card can no longer be managed — check key set, key version and security protocol before hammering a card. Version 3+ of the project adds a task-oriented CLI (`gpshell3`); the packaged 1.x line documented here is the script-file interface.

## Typical use cases

### Install a Java Card applet from its CAP file

Write a script (here `install_applet.txt`) that connects, opens the secure channel with the card's transport keys, and installs:

```
mode_211
enable_trace
establish_context
card_connect
select -AID a000000003000000
open_sc -security 3 -keyind 0 -keyver 0 -mac_key 404142434445464748494a4b4c4d4e4f -enc_key 404142434445464748494a4b4c4d4e4f
install -file helloworld.cap -nvDataLimit 2000 -instParam 00 -priv 2
card_disconnect
release_context
```

then run it:

```
gpshell install_applet.txt
```

Outcome: gpshell executes the script line by line, printing each APDU exchange (`enable_trace`) — the SCP02 channel opens with the given keys and `helloworld.cap` is loaded and made selectable. Afterwards the applet answers selection by its AID from any host application.

### Delete an applet and its package

Reuse the preamble but replace the tail with deletions:

```
mode_211
establish_context
card_connect
select -AID a000000003000000
open_sc -security 1 -keyind 0 -keyver 0 -mac_key 404142434445464748494a4b4c4d4e4f -enc_key 404142434445464748494a4b4c4d4e4f
delete -AID D0D1D2D3D4D50101
delete -AID D0D1D2D3D4D501
card_disconnect
release_context
```

Outcome: the applet instance (first AID) and then its package (second AID) are removed from the card — the cleanup step when re-installing during development, or when a card full of test applets needs resetting.

### Verify reader, card and keys before provisioning

Run any script with `enable_trace` but stop after the `select`/`open_sc` lines.

Outcome: the trace shows whether the PC/SC reader sees the card, whether the card manager AID selects, and whether mutual authentication succeeds with the configured key set — the cheap sanity check that prevents burning a card's retry counter with bad keys.

*No corpus evidence — derived from upstream documentation.*
