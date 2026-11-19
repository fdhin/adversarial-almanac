---
name: "chromensics"
categories:
  - blackarch-windows
version: "1.0"
homepage: "https://sourceforge.net/projects/chromensics/"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/Chromensics"
github-stars: 0
github-pushed: "2019-03-23"
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

# chromensics

> Windows GUI that reads every artifact out of a Google Chrome profile directory — without running the browser — and exports findings to SQLite or a PDF report.

*No corpus evidence — derived from upstream documentation (the project's user manual and published binary).*

## What it's for

A Google Chrome forensics GUI for Windows by Nishant Grover. Instead of parsing the profile's databases by hand, it loads a Chrome installation directory and presents all of its artifacts per profile in a tabbed interface with a search box, so an examiner can browse what a user did without launching (and altering) the browser. Evidence intake is the point: autoload the examiner's own installation (Ctrl+L — Chrome must be fully closed), load a `User Data` directory copied from another machine (Ctrl+O; default location `C:\Users\<user>\AppData\Local\Google\Chrome\User Data`), or attach a mounted evidence drive and pick the user from it (Ctrl+D). A loaded profile set can be exported to a single `.sqlite` file for sharing (Ctrl+E) and re-imported elsewhere (Ctrl+Q), and selected rows from the tabs can be generated into a PDF report with case metadata (Ctrl+P). It is a GUI-only executable with no command-line interface, so it cannot be scripted or batched; its output artifacts (the SQLite export and the PDF report) are what feed downstream tools.
