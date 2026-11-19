---
name: "redeye"
categories:
  - blackarch-misc
version: "0.9.4"
homepage: "https://github.com/cisagov/RedEye"
packaged-by: [blackarch]
blackarch-builddate: "2024-10-29"
build-system: scripts
license: BSD-3
meta-checked: "2026-11-09"
github-repo: "cisagov/RedEye"
github-stars: 2768
github-pushed: "2023-10-20"
github-archived: true
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# redeye

> CISA/PNNL's self-hosted web app for replaying, annotating and presenting red-team C2 logs such as Cobalt Strike traffic.

## What it's for

RedEye is a local server plus browser UI built by CISA and Pacific Northwest National Laboratory to make red-team command-and-control activity digestible: it parses C2 logs (bundled parsers include `cobalt-strike-parser`), visualizes hosts, beacons and commands, and lets operators tag and comment individual activities, then assemble presentations for stakeholders instead of scrolling thousands of log lines. A curated campaign exports as a standalone `.redeye` file that a Blue Team can open in a simplified read-only mode for reporting and remediation. The upstream repository is archived and in maintenance mode — bug fixes only, no new features. It is an analysis and reporting aid, not a C2 framework or scanner; the binary exposes only server-startup options (`./RedEye -h` lists them).

## Typical use cases

### Start in Red Team mode

Run the full-featured app for importing and curating your own C2 logs.

```
./RedEye --redTeam --password 'Password1'
```

Outcome: starts the RedEye server (a password is mandatory in Red Team mode), serves the web app at http://127.0.0.1:4000 and opens it in the default browser, ready to upload C2 logs or example datasets.

### Serve the UI on a custom port

Keep 4000 free or run alongside another instance.

```
./RedEye --redTeam --password 'Password1' --port 8443
```

Outcome: same as Red Team mode but the web app is served on port 8443; other server parameters (`--parsers`, `--childProcesses`) can also be set in a `config.json` next to the binary.

### Review an exported campaign read-only

Hand a curated campaign to a Blue Team without exposing editing features.

```
./RedEye
```

Outcome: starts in Blue Team mode (the default, no password); if a `campaigns/` folder next to the binary contains `.redeye` export files they are imported automatically and shown in the simplified read-only UI.

*No corpus evidence — derived from upstream documentation.*
