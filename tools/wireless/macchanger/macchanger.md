---
name: "macchanger"
categories:
  - 802-11
  - sniffing-spoofing
version: "1.7.0-6"
homepage: "https://www.gnu.org/software/macchanger"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2024-07-12"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "alobbs/macchanger"
github-stars: 712
github-pushed: "2021-08-23"
github-archived: false
github-issues: 72
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# macchanger

> Views and changes the MAC address of network interfaces.

## What it's for

GNU MAC Changer (Alvaro Lopez Ortega) shows an interface's current and
permanent (burned-in) MAC and replaces the current one with a chosen,
fully random, or real-vendor value. Typical uses are presenting a
different identity to networks that filter or remember MAC addresses,
and randomizing before association. It operates on the running
interface only — changes do not survive a reboot or driver reload, and
most drivers require the interface to be down (`ip link set wlan0
down`) before the change takes.

## Typical use cases

### Show the current and permanent MAC

Read both addresses for an interface.

```
macchanger -s wlan0
```

Outcome: prints `Current MAC` and `Permanent MAC` for the interface —
the default action when no other option is given, and the quickest way
to see whether the running address was already changed.

### Set a fully random MAC

Randomize the whole 48-bit address.

```
macchanger -r wlan0
```

Outcome: assigns a fully random MAC (locally-administered bit set) and
prints the old and new values.

### Randomize with a real vendor prefix

Make the address look like real hardware.

```
macchanger -A wlan0
```

Outcome: assigns a random MAC built from a real vendor OUI of any
kind; use `-a` instead to keep a vendor MAC of the same kind as the
interface's current one.

### Set a specific MAC

Choose the exact address to present.

```
macchanger -m aa:bb:cc:dd:ee:ff wlan0
```

Outcome: sets the interface MAC to the given value — for example to
clone an address a captive portal, MAC filter or session already
knows.

### Reset to the burned-in address

Undo changes and restore the factory value.

```
macchanger -p wlan0
```

Outcome: the interface MAC is reset to its original permanent hardware
value.

### List known vendors

Browse the OUI database.

```
macchanger -l
```

Outcome: prints the known vendor prefixes with descriptions; narrow it
with `-l=cisco` to find a specific vendor's OUI.

*No corpus evidence — derived from upstream documentation.*
