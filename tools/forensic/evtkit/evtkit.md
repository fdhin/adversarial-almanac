---
name: "evtkit"
categories:
  - blackarch-forensic
version: "8.af06db3"
homepage: "https://github.com/yarox24/evtkit"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "yarox24/evtkit"
github-stars: 18
github-pushed: "2016-03-29"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# evtkit

> Repair raw-acquired .evt Windows event log files (NT4–2003 era) so log parsers accept them.

## What it's for

Windows NT 4 through Server 2003 keep event logs in the legacy `.evt`
format, whose header carries a live-service state and whose end is
marked by a floating footer. A log copied while the EventLog service
was still running — the usual way these files are acquired during
incident response — retains that state, which is why acquired copies
often parse badly or not at all. evtkit (a single-file Python 2 script
by yarox24) repairs such acquisitions: it validates the `.evt` header
(magic and header size), sets the archive flag stored at offset 0x24,
and rewrites the floating footer, either in place or into copies of the
files. Files that fail validation (empty, wrong header, missing footer)
are reported with the reason instead of being rewritten. It has no
options beyond copy-directory and quiet mode, and it is not for the
newer `.evtx` format used from Vista on.

## Typical use cases

### Fix event log files in place

Repair logs you have already copied out of the evidence.

```
evtkit AppEvent.Evt SysEvent.Evt
```

Outcome: both files are patched where they stand (work on copies — the
upstream README's own warning), with verbose progress per file; invalid
inputs are named and their reason printed.

### Copy-and-fix a directory of logs

Repair without touching the originals.

```
evtkit --copy_to_dir=fixed evt_dir
```

Outcome: every `*.evt` file found under `evt_dir` is copied into
`fixed/` and repaired there, leaving the acquired originals untouched —
the safe mode for forensic work.

### Run quiet for scripted triage

Silence the per-file chatter in batch jobs.

```
evtkit -q --copy_to_dir=fixed evt_dir
```

Outcome: the same copy-and-fix repair with verbosity off, so only
errors surface — suitable for unattended sweeps over many log
directories.

*No corpus evidence — derived from upstream documentation.*
