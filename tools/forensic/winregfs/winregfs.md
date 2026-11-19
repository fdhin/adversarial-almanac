---
name: "winregfs"
categories:
  - forensics
version: "0.8-2"
homepage: "https://codeberg.org/jbruchon/winregfs"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali]
build-system: make
meta-checked: "2026-11-09"
github-repo: "ggthedev/winregfs"
github-stars: 0
github-pushed: "2016-09-06"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---
# winregfs

> Mounts Windows registry hive files as a FUSE filesystem: keys become directories, values become plain files.

## What it's for

winregfs (by Jody Bruchon) glues the chntpw registry library to FUSE: a hive file mounts as a tree of directories (keys) and small files (values), so ordinary shell tools — ls, cat, grep, echo — can inspect and edit an offline registry, where the classic `reged` editor's limited interactive interface makes scripting painful. Values carry type extensions (`.sz`, `.esz`, `.dw`, `.bin`, `.msz`), DWORDs appear as friendly hexadecimal text, forward slashes in names are escaped as `_SLASH_`, and a companion `fsck.winregfs` checks hive integrity. It is aimed at offline system repair and forensics on copies: the author flags it as not production-safe, writes are capped at 8 KiB per value, and unicode is unsupported — back up hives before writing through it.

## Typical use cases

### Mount a hive for shell access

Browse a registry with plain file commands.

```
mkdir reg && mount.winregfs /mnt/windows/Windows/System32/config/software reg/
```

Outcome: the SOFTWARE hive appears under `reg/` as directories; `ls reg/Microsoft/Windows/CurrentVersion/Run` lists autostart value files and `cat reg/Microsoft/Windows/CurrentVersion/Run/avast.sz` prints one — extension-less names also match (`cat .../Run/avast`), and `*` globs work.

### Check a hive's integrity

Triage a possibly damaged hive before other tools read it.

```
fsck.winregfs /mnt/windows/Windows/System32/config/system
```

Outcome: recursively walks every key and value, checking offsets, data reads and value types, then prints statistics and error counts; the exit status (0 = clean) can gate further processing in a script.

### Script an offline registry edit

Change a value with a shell redirect.

```
echo 00000001 > reg/Policies/Microsoft/Windows/System/SomePolicy.dw
```

Outcome: sets the REG_DWORD to 1 — DWORD value files take hex ASCII text rather than raw bytes; edits land directly in the hive file, so work on a copy and unmount cleanly afterwards (`fusermount -u reg`).

*No corpus evidence — derived from upstream documentation.*
