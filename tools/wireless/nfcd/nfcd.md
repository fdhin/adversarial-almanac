---
name: "nfcd"
gentoo-category: "net-wireless"
version: "9999"
homepage: "https://code.google.com/p/nfc-tools/wiki/nfcd"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: cmake
license: GPL-3
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# nfcd

> NFC daemon from the nfc-tools project that arbitrates reader access and exposes NFC targets to applications over D-Bus.

*No corpus evidence — derived from upstream documentation.*

## What it's for

nfcd is a system daemon from the nfc-tools project: it takes exclusive control of NFC hardware through libnfc and libfreefare and offers access to NFC devices and targets (tag read/write operations) to other applications via the system D-Bus bus, installing its policy as /etc/dbus-1/system.d/nfcd.conf. Clients talk to it through D-Bus rather than a command-line interface, and there is no documented CLI to show as a use case. The upstream source lived in the project's Google Code SVN repository, which is long gone, so the tool is effectively historical. Do not confuse it with the actively developed sailfishos/nfcd — a same-named but separate NFC daemon for Sailfish OS by different authors.
