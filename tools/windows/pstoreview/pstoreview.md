---
name: "pstoreview"
categories:
  - blackarch-windows
version: "1.0"
homepage: "https://vidstromlabs.com/freetools/pstoreview/"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-15"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# pstoreview

> Dumps the legacy Windows Protected Storage — IE AutoComplete and Outlook account credentials.

## What it's for

PStoreView (by Arne Vidström, ntsecurity.nu / vidstromlabs.com) lists the contents of the Protected Storage (PStore), the obfuscated credential store used by Windows NT 4.0, 2000, XP and 2003. It usually contains Internet Explorer AutoComplete data — saved form usernames and passwords — and Outlook/Outlook Express account names with their passwords. Running it in a user's session is a quick credential-recovery check on those systems; output goes to stdout as plain text, so it can be redirected to a file or paged. It is strictly a legacy tool: the Protected Storage API no longer exists on current Windows, where there is nothing for it to read.

## Typical use cases

### Dump saved credentials from Protected Storage

Run it in a Command Prompt under the target user's logged-in session.

```
pstoreview.exe
```

Outcome: prints every item held in Protected Storage, typically IE AutoComplete form entries and Outlook account names/passwords; append `> pstore.txt` to write the findings to a file instead of the console.

*No corpus evidence — derived from upstream documentation.*
