---
name: "autonessus"
categories:
  - blackarch-automation
version: "24.7933022"
homepage: "https://github.com/redteamsecurity/AutoNessus"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "redteamsecurity/AutoNessus"
github-stars: 214
github-pushed: "2019-11-08"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# autonessus

> Minimal CLI for the Nessus REST API that lists scans/policies and starts, stops, pauses, or resumes scans by ID, so scanning can be cron-driven.

## What it's for

AutoNessus wraps the Nessus 6/7 REST API in a single Python script so scans can be driven from the command line or a cron job instead of the web UI: list scans and policies with their IDs, then start, stop, pause, or resume a scan by ID — handy for fitting scans inside an agreed testing window. It operates only on scans that already exist in the Nessus instance; it cannot create scans or policies. Server URL and API credentials are not passed as arguments — they must be edited into the script itself (around lines 52–56) before first use. Note it is Python 2 code and predates current Nessus releases, so verify against your scanner version; by default it refuses to relaunch a scan whose status is already "completed" (the status list near line 272 controls that).

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### List scans and their IDs

Discover the scan IDs needed by every other flag.

```
autonessus -l
```

Outcome: prints all scans defined on the Nessus server together with their numeric scan IDs and statuses; grab the ID of the scan you want to drive.

### List scan policies

See which policy templates exist on the server.

```
autonessus -p
```

Outcome: prints the current policies configured on the Nessus instance (useful when planning new scans in the web UI).

### Start a scan by ID

Kick off a defined scan without opening the browser.

```
autonessus -sS 42
```

Outcome: the Nessus server launches scan 42 and the script reports the launch; combine with cron to start scans at the beginning of an approved testing window.

### Pause, resume, or stop a scan by ID

Control a running scan from a scheduled task.

```
autonessus -pS 42
```

Outcome: pauses scan 42; the sibling flags `-sR 42` (resume) and `-sP 42` (stop) bring it back up inside the testing window or halt it — e.g. pause at 17:00 and resume at 08:00 via cron entries.
