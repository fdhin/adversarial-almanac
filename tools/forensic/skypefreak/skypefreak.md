---
name: "skypefreak"
categories:
  - blackarch-forensic
version: "33.9347a65"
homepage: "http://osandamalith.github.io/SkypeFreak/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "OsandaMalith/SkypeFreak"
github-stars: 65
github-pushed: "2017-05-21"
github-archived: false
github-issues: 5
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# skypefreak

> Interactive forensic framework that mines the legacy Skype client's main.db.

## What it's for

SkypeFreak (by Osanda Malith) is a cross-platform (Windows, Linux, macOS;
Python 2.6 through 3.x) forensic framework for the classic desktop Skype
client's local SQLite database, `main.db`. It is menu-driven: after asking
which Skype username's profile to examine, it locates the profile database
(`~/.Skype/<username>/main.db` or the Windows equivalent), then parses account
profile details, contacts, call logs and chat messages, offering to write each
result set to disk. It reads only the local machine's profile data — it never
talks to Skype servers. It targets the pre-2017 client database; modern
Skype's cloud-centric data model is out of scope.

## Typical use cases

### Run an interactive examination

Pick one artifact category at a time from the menu.

```
python SkypeFreak.py
```

Outcome: prompts for the Skype username, locates the matching local profile
database, then presents the menu (1 Profile, 2 Contacts, 3 Calls, 4 Messages,
5 Generate Full Report, …); each selection prints the parsed records to the
console and offers to save them to a file.

### Generate a full report

Dump everything the framework can extract in one pass.

```
python SkypeFreak.py
```

Outcome: choose option 5 at the menu; the combined profile, contact, call and
message report is printed and, on confirmation, written to a filename you
supply — the deliverable to attach to a case file.

*No corpus evidence — derived from upstream documentation.*
