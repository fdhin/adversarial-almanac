---
name: "gpocrack"
categories:
  - blackarch-cracker
version: "3.cf63c86"
homepage: "https://github.com/MartinIngesen/gpocrack"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "MartinIngesen/gpocrack"
github-stars: 23
github-pushed: "2021-04-14"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gpocrack

> Decrypts a Group Policy Preferences `cpassword` blob into plaintext using Microsoft's published AES key.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Old Group Policy Preferences (GPP) — scheduled tasks, services, drive maps, local users — stored passwords in SYSVOL XML files "encrypted" with a static AES-256 key that Microsoft documented on MSDN; any authenticated domain user can read those files. gpocrack is a one-argument decryptor: hand it the base64 `cpassword` value pulled from such an XML file and it pads, decrypts with the well-known key, and prints the password instantly. It is a decryptor, not a cracker — nothing is guessed or brute-forced, so output is immediate. It does not search SYSVOL or parse XML for you: extract the attribute's value (from `Groups.xml`, `Services.xml`, `ScheduledTasks.xml`, ...) with grep or an editor first. On domains where the GPP patch (MS14-025) was applied long ago, new blobs no longer exist, but stale SYSVOL data lingers surprisingly often.

## Typical use cases

### Decrypt a GPP cpassword value

```
gpocrack LjFWQMzS3GWDeav7+0Q0oSoOM43VwD30YZDVaItj8e0
```

Outcome: prints `Password is: <plaintext>` on stdout. Missing base64 padding (`=`) is added automatically, so the raw attribute value can be pasted as-is. Run it once per `cpassword` attribute you find.
