---
name: "whapa"
categories:
  - blackarch-misc
version: "394.c17ca34"
homepage: "https://github.com/B16f00t/whapa"
packaged-by: [blackarch]
blackarch-builddate: "2026-08-02"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "B16f00t/whapa"
github-stars: 1699
github-pushed: "2026-08-02"
github-archived: false
github-issues: 68
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 9
gh-activity-checked: "2026-11-09"
---

# whapa

> Forensic toolset for decrypting and parsing WhatsApp databases from Android and iOS.

## What it's for

whapa is a set of Python 3 tools for WhatsApp forensics. The core parser
(`libs/whapa.py`) reads a decrypted msgstore.db (Android, current and legacy
schemas) or ChatStorage.sqlite (iOS), resolves bare numbers to contact names
via wa.db, and produces interactive HTML, printable and CSV reports of chats,
calls and statuses. Sibling tools decrypt crypt12/crypt14/crypt15 backups
(whacipher), pull backups from the tied Google Drive or iCloud account
(whagodri, whacloud), merge databases from different dates (whamerge) and
parse chats exported from the app (whachat). It analyzes data you already
hold; acquiring the handset image is out of scope. A graphical interface
(`whapa-gui.py`) wraps the whole set.

## Typical use cases

### See what chats a database contains

```
python3 libs/whapa.py msgstore.db -i 3
```

Outcome: lists the conversations in the database with participants and counts — the inventory step before pulling any specific chat (`-i 1` shows status messages, `-i 2` the call log, `-i 4` unknown message types).

### Decrypt an encrypted backup

```
python3 libs/whacipher.py -f msgstore.db.crypt15 -d key -o msgstore.db
```

Outcome: decrypts a crypt15 backup using the 158-byte key file (or the 64 hex-character root key) into a plain SQLite database ready for the parser; crypt12 and crypt14 are detected automatically.

### Produce a full report with contact names and media

```
python3 libs/whapa.py msgstore.db -m -a -wa wa.db -mp /path/to/WhatsApp -r EN -o ./report
```

Outcome: writes an interactive report of all conversations under ./report, with phone numbers resolved to names from wa.db and attachments copied from the phone's WhatsApp media folder.

### Recover deleted records

```
python3 libs/whapa.py msgstore.db -c -o ./carved
```

Outcome: carves deleted records out of the database's free space (via the bundled undark) into ./carved for review alongside the live data.

### Download backups from the account's Google Drive

```
python3 libs/whagodri.py -sd -o ./backups
```

Outcome: using the credentials configured in cfg/settings.cfg, downloads the WhatsApp databases found in the account's Google Drive backup to ./backups (`-s` pulls media as well, `-i` and `-lw` only list what exists).

*No corpus evidence — derived from upstream documentation.*
