---
name: "chrome-decode"
categories:
  - blackarch-windows
version: "0.1"
homepage: "http://packetstormsecurity.com/files/119153/Chrome-Web-Browser-Decoder.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/chrome_decode"
github-stars: 0
github-pushed: "2019-03-23"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# chrome-decode

> Recover the autocomplete passwords stored in Google Chrome's "Login Data" database by decrypting them through Windows DPAPI.

*No corpus evidence — derived from the packaged upstream source (Google Chrome Decoder v1.0, 2012, Kevin Devine, distributed via Packet Storm).*

## What it's for

A minimal 2012 demonstration (single C++ file) of how Chrome stored saved passwords at the time: each `password_value` blob in the `logins` table of the `Login Data` SQLite database was encrypted with `CryptUnprotectData`, i.e. only protected by the Windows account's DPAPI master key. The tool opens that database, walks every login row and prints the decrypted username, password and realm. It runs as the logged-on user against the local Chrome profile by default, or against a `Login Data` file copied out of another profile. Because decryption happens under the user's DPAPI context, output from a foreign machine is only readable when run in that user's context on that machine. It predates Chrome 80's move to an app-key/AES-GCM scheme, so on current Chrome builds the blobs no longer decrypt — it is a teaching and legacy-image audit artifact, not a current browser password dumper.

GitHub: third-party mirror of the PacketStorm distribution (chrome_decode source and binary, hash-identical).

## Typical use cases

### Dump the saved passwords of the local Chrome profile

```
chrome_decode.exe
```

Outcome: resolves `%LOCALAPPDATA%\Google\Chrome\User Data\Default\Login Data`, decrypts every `logins` row via `CryptUnprotectData` and prints a Username / Password / Realm table (or `No entries found` when the database is empty), then waits for a keypress.

### Decrypt a copied Login Data database

```
chrome_decode.exe "C:\Users\user\Desktop\Login Data"
```

Outcome: opens the given SQLite file instead of the live profile and prints the same Username / Password / Realm table; `<decryption failed>` in the password column means the blob could not be decrypted in the current Windows context. Useful when examining a Chrome profile folder exported from an acquired image while operating as the profile's owner.
