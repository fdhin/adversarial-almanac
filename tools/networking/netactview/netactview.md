---
name: "netactview"
categories:
  - blackarch-networking
version: "0.6.4"
homepage: "http://netactview.sourceforge.net/index.html"
packaged-by: [blackarch, aur]
blackarch-builddate: "2019-05-06"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "mihaivzr/netactview"
github-stars: 24
github-pushed: "2022-08-07"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# netactview

> GUI netstat for Linux: a continuously refreshed connections table joined with the owning process.

## What it's for

Net Activity Viewer (Mihai Varzaru) is a GTK 2 desktop application that shows the machine's network connections — the information netstat prints once — updated live and joined with per-socket process information via libgtop, so each connection shows the program that owns it. It resolves remote addresses to hostnames, supports sorting and filtering, refreshes automatically, and can export the current table to CSV. It is purely an observation tool: no packet capture, no filtering or firewalling. Since 2022 the upstream author has publicly sought new maintainers; the GitHub repository is his mirror of the SourceForge project, which remains the canonical home.

## Typical use cases

### Launch the live connections viewer

```
netactview
```

Outcome: opens the connections window listing local and remote address, state, and owning process for every socket, refreshing on its own; filter the view to a host or port inline and export what you see to CSV. Standard GTK/GLib options (for example `--help`) are accepted on the command line.

*No corpus evidence — derived from upstream documentation.*
