---
name: "ghunt"
categories:
  - blackarch-recon
version: "v2.2.0.r30.g5ee8939"
homepage: "https://github.com/mxrch/GHunt"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-04-12"
build-system: python
license: AGPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "mxrch/GHunt"
github-stars: 19804
github-pushed: "2026-04-10"
github-archived: false
github-issues: 77
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
---

# ghunt

> mxrch's offensive Google OSINT framework (v2): authenticate once with your Google cookies, then dig into email addresses, Gaia IDs, Drive documents and WiFi BSSIDs from one async CLI.

## What it's for

GHunt v2 (Python 3.10+, AGPL) is a modular framework for extracting information Google holds about people and artifacts. Its power comes from operating as an authenticated Google account: after a one-time `ghunt login` (cookies pasted in, or captured by the GHunt Companion browser extension), each module queries Google endpoints directly — `email` maps an email address to the associated Google account (name, profile picture, Gaia ID, public artifacts), `gaia` does the reverse from a numeric Gaia ID, `drive` enumerates metadata for a Drive file or folder, `geolocate` resolves a WiFi BSSID to coordinates via Google's location data, and `spiderdal` walks Digital Assets Links to find apps and assets related to a target. Every data-returning module supports `--json` export, and the whole thing is also importable as a Python library. It is OSINT over your own authenticated session — use it on accounts and data you are authorized to investigate, per its license terms.

## Typical use cases

### Authenticate the tool to Google

Do the one-time setup before any lookups.

```
ghunt login
```

Outcome: offers login methods — put GHunt in listening mode for the Companion extension, paste base64-encoded cookies, or enter cookies manually — and saves the session for all later module runs.

### Investigate an email address

Identify the Google account behind an address.

```
ghunt email user@example.com
```

Outcome: prints the Google account data attached to the email — name, profile picture URL, Gaia ID, Google reviews/photos and other public traces — or reports that the address has no attached account.

### Export an email lookup as JSON

Feed the results into downstream tooling.

```
ghunt email user@example.com --json user_data.json
```

Outcome: performs the same lookup but writes the structured results to `user_data.json` (`--json` works on the gaia, drive and geolocate modules too).

### Resolve a Gaia ID back to a person

Work from a numeric identifier.

```
ghunt gaia GAIA_ID
```

Outcome: prints the Google account profile attached to the given numeric Gaia ID — the reverse path when you only captured an ID from logs, a document or another GHunt run.

### Geolocate a WiFi access point

Turn a BSSID into coordinates.

```
ghunt geolocate AA:BB:CC:DD:EE:FF
```

Outcome: queries Google's BSSID location data for the access point and prints its estimated coordinates — approximate, not survey-grade.

### Inspect a Drive file or folder

Enumerate a shared document's metadata.

```
ghunt drive FILE_ID
```

Outcome: prints the metadata GHunt can extract for the given Drive file or folder ID (name, ownership, sharing state and related artifacts); the `spiderdal` module similarly takes a target and walks its Digital Assets Links to list associated apps and assets.

*No corpus evidence — derived from upstream documentation.*
