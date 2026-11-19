---
name: "grokevt"
categories:
  - blackarch-forensic
version: "0.5.0.6.1.r0.gfb16294"
homepage: "https://projects.sentinelchicken.org/grokevt/"
packaged-by: [blackarch, kali]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "ecbftw/grokevt"
github-stars: 10
github-pushed: "2024-07-12"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# grokevt

> Convert classic Windows NT/2000/XP/2003 .EVT event logs into human-readable CSV, with real event-message text substituted from the source system's message DLLs.

## What it's for

GrokEVT (Timothy D. Morgan, Sentinel Chicken Networks) is a Python toolset for the classic pre-Vista EVT event log format. Event IDs alone are meaningless without the message templates, so GrokEVT works in two phases: `grokevt-builddb` builds a message database from a mounted Windows partition (registry hive plus the event-source message DLLs), and `grokevt-parselog` then decodes EVT files into CSV on stdout with the actual localized message strings filled in. Supplementary scripts carve log fragments out of raw media (`grokevt-findlogs`), attach carved or extra logs to a database (`grokevt-addlog`), and pull message resources out of PE files (`grokevt-ripdll`, `grokevt-dumpmsgs`). It does not support the Vista+ EVTX format — for that use dedicated EVTX parsers. Upstream development is dormant; the project site remains the authoritative reference.

## Typical use cases

### Build the message database from a mounted Windows partition

After copying `/etc/grokevt/systems/example` to a new profile directory and editing its registry path, drive mappings and `%SystemRoot%` variables to match the mounted Windows tree:

```
grokevt-builddb winxp /tmp/evt-db
```

Outcome: `/tmp/evt-db` is populated with the event-source/message mappings needed to decode logs from that system; `-v` prints progress and `-c CSID` overrides the ControlSet chosen from the registry.

### Export an event log to CSV

Decode one log from a built database.

```
grokevt-parselog /tmp/evt-db System > system.csv
```

Outcome: the System log renders as CSV on stdout with message text resolved; run `grokevt-parselog -l /tmp/evt-db` first to list the log types the database offers (Application, Security, System and any added types).

### Carve .EVT fragments from a raw image

Locate event logs in unallocated space, memory dumps or damaged media.

```
grokevt-findlogs disk.raw
```

Outcome: reports recoverable EVT fragments found in the raw file (add `-v` for detail); extract the indicated ranges and parse them, or attach them to a database directly with `grokevt-addlog /tmp/evt-db carved.evt CarvedLogs System`, which registers the carved file under the log type `CarvedLogs` using the existing `System` message templates.

### Extract message templates from a third-party DLL

Decode events from non-Microsoft event sources whose DLLs are not in the standard set.

```
grokevt-ripdll appmsg.dll appmsg.db
```

Outcome: `appmsg.db` contains the message resources extracted from the PE file; `grokevt-dumpmsgs appmsg.db` prints them so you can confirm the templates before relying on them for decoding.

*No corpus evidence — derived from upstream documentation.*
