---
name: "gplist"
categories:
  - blackarch-windows
version: "1.0"
homepage: "https://vidstromlabs.com/freetools/gplist/"
packaged-by: [blackarch]
blackarch-builddate: "2024-10-03"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# gplist

> Lists the Group Policies applied to a Windows host, in application order.

## What it's for

GPList (Vidstrom Labs, by Alrik Vidstrom) answers a quick triage question
during host review: which Group Policy Objects are actually applied to
this machine, and in what order. It is a small flagless console utility —
running it with no arguments from a Command Prompt prints the applied
GPOs, first-applied first, which matters because later policies can
override earlier ones. It has no remote mode, no filtering options and no
editing capability: it is read-only enumeration of the local applied
policy state, useful to confirm GPO delivery after changes or during
incident triage.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### List applied Group Policies

See which GPOs the host applies and in which order.

```
GPList.exe
```

Outcome: prints the applied Group Policies to the console in application
order (the GPO applied first is listed first), which reveals both the
policy set and the precedence used to resolve conflicts.

### Save the applied policy list to a file

Capture the listing as evidence or for later comparison.

```
GPList.exe > gpos.txt
```

Outcome: writes the same applied-GPO listing to `gpos.txt` instead of the
console, giving a snapshot you can diff against a later run or attach to
a report.
