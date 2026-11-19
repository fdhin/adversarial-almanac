---
name: "regreport"
categories:
  - blackarch-windows
version: "1.6"
homepage: "https://www.gaijin.at/dlregreport.php"
packaged-by: [blackarch]
blackarch-builddate: "2019-12-24"
build-system: windows
meta-checked: "2026-11-09"
github-repo: "Seabreg/registryreport"
github-stars: 1
github-pushed: "2019-03-24"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# regreport

> Gaijin's RegistryReport: turns offline Windows NT registry hives into a searchable report of OS, software and user information.

GitHub: third-party mirror of the gaijin.at RegistryReport distribution.

## What it's for

RegistryReport (gaijin.at) reads the offline registry files of a Windows NT 5 or higher system — SYSTEM, SOFTWARE, SAM and NTUSER.DAT — and compiles a report about the operating system, the installed software, user information and more. It works only on hive files, never against the registry of the running OS, which makes it a companion to forensic suites such as Guidance EnCase or X-Ways Forensics: pull the hives in your imaging workflow and analyze them afterwards. The GUI lets you search, print and save or copy the report as plain text or rich text (RTF). Version 1.6 is freeware, ships portable with no installation, and is localized in roughly a dozen languages. Analysis is GUI-driven — its command-line parameters (added in v1.3.4/1.3.6) only pre-open hive files and pre-set the report file name — so there is no documented command syntax to script report generation with.

*No corpus evidence — derived from upstream documentation. Description-only: GUI application — its command-line parameters only pre-open hive files and pre-name the report, with no documented syntax for runnable examples.*
