---
name: "regview"
categories:
  - blackarch-windows
version: "1.3"
homepage: "https://www.gaijin.at/en/dlregview.php"
packaged-by: [blackarch]
blackarch-builddate: "2018-09-11"
build-system: windows
meta-checked: "2026-11-09"
github-repo: "Seabreg/regview"
github-stars: 0
github-pushed: "2019-03-24"
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

# regview

> GUI viewer for raw Windows NT registry hive files (SYSTEM, SOFTWARE, SAM, NTUSER.DAT, ...) pulled off a machine.

## What it's for

RegView — distributed by Gaijin.at as "RegistryViewer" — is a Windows GUI
program for offline inspection of registry hives: it opens the raw
Windows NT 5 (Windows 2000 and higher) hive files SYSTEM, SOFTWARE, SAM,
SECURITY, NTUSER.DAT, USERDIFF and DEFAULT via drag & drop and presents
folders, keys and values in a tree modeled on the Windows Registry
editor. Values can be searched and displayed in hexadecimal form. It
deliberately does not open the registry of the running operating system,
so it is aimed at analyzing hive files copied from disk images or other
machines. The tool is pure GUI with no command-line interface. The
BlackArch package ships the standalone executable under
`/usr/share/windows/regview/`; the original Gaijin.at download page is no
longer online (archived copies describe the tool), while the download
archive itself is still fetched from the author's site. Not to be
confused with the unrelated same-named Rust console registry viewer by a
different author.

GitHub: third-party mirror of Gaijin.at RegistryViewer (Seabreg/regview).

*No corpus evidence — derived from upstream documentation.*
