---
name: "nirsoft"
categories:
  - blackarch-windows
version: "1.30.23"
homepage: "https://www.nirsoft.net"
packaged-by: [blackarch]
blackarch-builddate: "2026-04-29"
build-system: windows
meta-checked: "2026-11-09"
github-repo: "ScoopInstaller/Nirsoft"
github-stars: 174
github-pushed: "2026-11-05"
github-archived: false
github-issues: 8
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 482
gh-activity-checked: "2026-11-09"
---

# nirsoft

> Suite hub for NirSoft — Nir Sofer's large collection of standalone Windows freeware utilities used in security work.

## What it's for

NirSoft publishes hundreds of small, single-purpose Windows tools, and many are standard items in a security toolkit: password and key recovery (ProduKey for Windows/Office keys, WirelessKeyView for saved Wi-Fi credentials, Mail PassView), browser and system inspection (BrowsingHistoryView, DriverView, ServiWin) and forensic helpers. Each tool is a standalone GUI executable with no installer. What makes the suite scriptable is a shared command-line export convention — most utilities document the same `/stext`, `/stab`, `/scomma`, `/stabular`, `/shtml`, `/sverhtml` and `/sxml` save options, plus `/sort`/`/nosort` — so a GUI tool can be driven headlessly for triage. This note is the hub; each utility is documented on its own page under nirsoft.net/utils/, and the `github-repo` above is the Scoop packaging manifest collection, not an upstream source tree.

## Typical use cases

### Export a tool's report from the command line

Get the data out without touching the GUI — shown for DriverView, the loaded-driver lister.

```
DriverView.exe /stext drivers.txt
```

Outcome: writes every loaded driver with its load address, description, version and company to drivers.txt; the same flag works in place on the other NirSoft utilities that document the shared export options.

### Produce a sorted HTML report

Save a shareable report ordered by chosen columns.

```
DriverView.exe /shtml drivers.html /sort 2 /sort ~1
```

Outcome: writes drivers.html (horizontal HTML table) sorted ascending by column index 2, then descending by column index 1 — prefix a column with `~` to reverse it, or pass column names like `"Company"` instead of indexes.

### Capture a machine-readable listing for triage

Emit CSV instead of text for spreadsheet or script consumption.

```
DriverView.exe /scomma drivers.csv /nosort
```

Outcome: writes a comma-delimited drivers.csv in the tool's natural enumeration order (`/nosort` disables sorting), ready for diffing against a baseline inventory.

*Corpus evidence: genuine suite references mined from the corpus (NirSoft utilities downloaded from nirsoft.net for lab work); export flags verified against upstream tool pages.*
