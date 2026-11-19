---
name: "hackbrowserdata"
categories:
  - windows
version: "git (2026-11-04)"
homepage: "https://github.com/moond4rk/hackbrowserdata"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: go
meta-checked: "2026-11-09"
github-repo: "moond4rk/hackbrowserdata"
github-stars: 14688
github-pushed: "2026-11-09"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 148
gh-activity-checked: "2026-11-09"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 20
---

# hackbrowserdata

> Decrypts and exports passwords, cookies, history and more from Chromium browsers, Firefox and Safari on Windows, macOS and Linux.

## What it's for

HackBrowserData (moonD4rk) is a Go command-line tool that extracts and
decrypts browser data — passwords, cookies, bookmarks, history,
downloads, credit cards, extensions, local and session storage — from
Chromium-based browsers and Firefox on Windows, macOS and Linux, plus
Safari on macOS. Its distinguishing capability is cross-host decryption:
on the origin host you export the browser master keys (`dumpkeys`) and
pack only the decryption-relevant profile files (`archive`), then decrypt
the copy offline on any analyst host with `restore` — nothing
platform-bound (DPAPI, Keychain, App-Bound Encryption) has to leave the
origin. Intended for authorized security research, incident response and
DFIR; the exported keys and data are secrets and must be handled
accordingly.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Dump everything from all installed browsers

Run the default extraction on a host you control.

```
hack-browser-data
```

Outcome: auto-detects installed browsers and profiles, decrypts every
supported category, and writes per-category JSON files into the default
`results/` output directory.

### Target one browser and export specific categories

Collect exactly the data an investigation needs, in a spreadsheet-ready
form.

```
hack-browser-data dump -b chrome -c password,cookie -f csv -d case01-out
```

Outcome: extracts only Chrome passwords and cookies and writes them as
CSV files into `case01-out/`, keeping the output set small and directly
reviewable.

### List detected browsers and profiles

Discover what the tool can see before a full dump.

```
hack-browser-data list
```

Outcome: prints each detected browser with its profile paths — useful to
confirm coverage or to find the exact profile path for `-p` before
extracting.

### Decrypt collected data on another host

Move decryption off the origin machine.

```
hack-browser-data dumpkeys -o keys.json
hack-browser-data archive -o browser-data.zip
hack-browser-data restore --keys keys.json --data-zip browser-data.zip
```

Outcome: the first two commands run on the origin host and produce
`keys.json` (portable master keys, written 0600 — treat as a secret) and
`browser-data.zip`; after copying both to the analyst host, `restore`
decrypts the packed profiles offline and writes the same csv/json
output formats as a local dump.
