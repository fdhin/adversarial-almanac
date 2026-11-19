---
name: "lazagne"
categories:
  - blackarch-forensic
version: "v2.4.7.r6.g858ff6f"
homepage: "https://github.com/AlessandroZ/LaZagne"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: LGPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "AlessandroZ/LaZagne"
github-stars: 11072
github-pushed: "2025-09-18"
github-archived: false
github-issues: 19
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# lazagne

> Harvest credentials cached on a local machine — browsers, mail clients, databases, sysadmin tools, wifi, and the Windows credential stores.

## What it's for

The LaZagne project by AlessandroZ is the classic post-exploitation credential dumper: each supported application stores its passwords differently (plaintext files, Windows APIs, custom algorithms, SQLite databases), and LaZagne knows how to locate and decrypt them. It runs on Windows, Linux and macOS with module categories such as browsers, mails, chats, databases, sysadmin (FileZilla, OpenSSH, WinSCP, ...), memory (KeePass and mimikatz-style dumps), wifi, and windows (credential manager, autologon, hashdump, LSA secrets, vault files). It decrypts what is already stored locally — it does not brute-force anything — and wifi/Windows-secrets modules need elevated privileges. It ships as `laZagne.exe` on Windows and the `laZagne.py`/`lazagne` script on Linux.

## Typical use cases

### Run every module

One-shot sweep of all credential sources on the host.

```
lazagne all
```

Outcome: every module runs in turn and found credentials are printed grouped by application, with a summary of failures at the end; on Windows the equivalent command is `laZagne.exe all`.

### Target one category or one application

Narrow the run to a single module or a single piece of software.

```
lazagne browsers -firefox
```

Outcome: only browser credential stores are touched — in this form only Firefox's — shortening runtime and noise compared to `all`; other category names include `mails`, `sysadmin`, `databases`, `wifi` and `chats`.

### Write findings to a file

Persist results as text, JSON, or both.

```
lazagne all -oA -output /home/user/loot
```

Outcome: every password found is written to the given directory — `-oN` for plain text, `-oJ` for JSON, `-oA` for both — instead of only scrolling past on stdout.

### Dump the Windows credential stores

Collect Windows-internal secrets (requires elevation).

```
laZagne.exe windows
```

Outcome: run from an elevated shell (UAC consent or sudo equivalent), extracts the Windows credential manager entries, autologon password, NT/LM hashes, LSA secrets and vault files; without admin privileges these modules return nothing. Wifi keys on Linux similarly need `sudo lazagne wifi`.

### Decrypt with a known user password

Supply the login password so DPAPI-protected secrets can be opened.

```
laZagne.exe all -password Password1
```

Outcome: the given Windows user password is used to decrypt domain and DPAPI-protected credentials; without it the tool falls back to trying every password it has already found. Add `-vv` for debug detail, or `-quiet` to suppress console output entirely while writing results with `-oN`.

*No corpus evidence — derived from upstream documentation.*
