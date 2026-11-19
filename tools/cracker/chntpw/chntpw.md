---
name: "chntpw"
categories:
  - passwords
version: "140201-5"
homepage: "https://pogostick.net/~pnh/ntpasswd/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2024-09-07"
build-system: make
license: GPL, LGPL
meta-checked: "2026-11-09"
github-repo: "rescatux/chntpw"
github-stars: 80
github-pushed: "2020-08-31"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# chntpw

> Offline editor for Windows SAM hives: list local users and blank or change their passwords without knowing the old ones.

## What it's for

chntpw (Petter Nordahl-Hagen's Offline NT Password Editor) edits a Windows NT-family SAM user database file directly, with no running Windows and no old password required — the usual workflow is booting a rescue Linux on the machine (or mounting its disk elsewhere) and pointing the tool at `Windows/System32/config/SAM`. It can blank a user's password, set a new one, promote a user to administrator and unlock accounts, and it embeds a registry editor with write access to any hive. The same source ships sibling tools for scripting: `reged` (registry import/export), `sampasswd` and `samusrgrp` (scriptable password and group operations). It resets rather than cracks — the old password is never recovered — and it targets local accounts in NT-through-Windows-8-era SAMs; Microsoft-account (online) logons are out of scope.

## Typical use cases

### List the users in a SAM hive

See the local accounts before editing anything.

```
chntpw -l /mnt/windows/Windows/System32/config/SAM
```

Outcome: one human-readable block per local account with its RID, group membership hints and whether a password is set, then exits without writing anything.

### Reset a user's password interactively

Blank or replace the password of one account.

```
chntpw -u 0x3e9 /mnt/windows/Windows/System32/config/SAM
```

Outcome: opens the edit menu for the user with RID 0x3e9 — blank the password, set a new one, promote to admin or unlock — and writes the change on exit. The RID form is preferred: lookup by name fails for usernames with international characters.

### Open the boot-CD-style interactive menu

Edit passwords and registry through the full menu system.

```
chntpw -i -L /mnt/windows/Windows/System32/config/SAM
```

Outcome: the interactive menu covering password edits and registry paths loads the given hive(s); `-L` appends the names of any changed hive files to `/tmp/changed`, which wrapper scripts use to know what to save.

### Edit a registry hive directly

Make registry changes without booting Windows.

```
chntpw -e /mnt/windows/Windows/System32/config/SYSTEM
```

Outcome: a command-line registry editor opens on the hive; `?` lists its commands, and edits are saved back to the hive file on quit.

*No corpus evidence — derived from upstream documentation.*
