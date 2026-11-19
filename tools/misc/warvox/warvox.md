---
name: "warvox"
gentoo-category: "net-misc"
version: "9999"
homepage: "http://warvox.org"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: python
license: BSD
meta-checked: "2026-11-09"
github-repo: "rapid7/warvox"
github-stars: 209
github-pushed: "2018-04-12"
github-archived: true
github-issues: 14
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# warvox

> War-dialing suite that bulk-calls number ranges over VoIP (IAX2) and classifies what answers by analyzing the recorded audio.

## What it's for

WarVOX is a VoIP war dialer: instead of a modem bank, it uses an IAX2 VoIP
provider to call large blocks of phone numbers cheaply and quickly, records
the audio of each answered call, and applies signal analysis to classify the
line — modem, fax, DTMF tones, voicemail, voice, silence. Results land in a
PostgreSQL-backed Rails web interface where you compare normal versus
anomalous line populations and dig out the carriers and modems worth
attacking. It replaces the dialing hardware of classic wardialing with a
single server and a VoIP account. Upstream is unmaintained (final release
2018) and requires Ruby and PostgreSQL.

## Typical use cases

### Start the WarVOX web interface

```
bin/warvox
```

Outcome: launches the WarVOX daemons and web UI on http://127.0.0.1:7777/ — providers, projects and dialing jobs are configured from that interface.

### Bind the web interface to all interfaces

```
bin/warvox --address 0.0.0.0
```

Outcome: same daemons but the web UI listens on every interface, reachable from another workstation on the network.

### Create the initial admin account

```
bin/adduser admin randompass
```

Outcome: writes an administrator login to the WarVOX database; use it to sign into the web interface on first run.

*No corpus evidence — derived from upstream documentation.*
