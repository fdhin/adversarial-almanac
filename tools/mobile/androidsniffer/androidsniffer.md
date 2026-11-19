---
name: "androidsniffer"
categories:
  - blackarch-mobile
version: "0.1"
homepage: "http://packetstormsecurity.com/files/97464/Andr01d-Magic-Dumper.1.html"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/AndroidSniffer"
github-stars: 1
github-pushed: "2019-09-27"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# androidsniffer

> Perl script that mines a Nandroid `data.tar` dump for stored account passwords, call logs, contacts and Wi-Fi credentials.

## What it's for

androidsniffer (the "Andr01d Magic Dumper" Perl script by Sw1tCh) is an offline forensic helper: it takes a `data.tar` pulled from a Nandroid backup of an Android device — a copy of the `/data` partition — and reads the interesting bits out of its SQLite databases and config files. It can dump third-party account passwords stored in `system/accounts.db`, the call log, phone and email contacts, contact groups, and saved wireless networks from `misc/wifi/wpa_supplicant.conf`. It never touches a live device and, despite the name, is not a network sniffer — everything it reports comes from the tar archive you hand it. Interaction is a numbered console menu; the archive is unpacked into a `_temp/` working directory which is deleted on clean exit. Requires Perl `DBI`/`DBD::SQLite` for the database dumps.

## Typical use cases

### Analyze a Nandroid data dump

Extract stored credentials and personal data from a tar of the device's `/data` partition.

```
androidsniffer data.tar
```

Outcome: the script validates that the argument is an existing file ending in `.tar` (otherwise it prints a usage banner and exits), unpacks it into `./_temp/`, and opens a numbered menu: 1 search 3rd-party passwords, 2 dump call log, 3 dump contacts, 4 dump email contacts, 5 dump contact groups, 6 dump wireless configuration, q exit. Each option prints rows read directly from the dump's SQLite databases or `wpa_supplicant.conf`; choosing `q` removes `_temp/` and quits.

*No corpus evidence — derived from upstream documentation.*

GitHub: third-party mirror of the PacketStorm upstream (`AndroidSniffer.pl` byte-identical to the packaged distfile).
