---
name: "windowsspyblocker"
categories:
  - blackarch-windows
version: "4.38.0"
homepage: "https://github.com/crazy-max/WindowsSpyBlocker/"
packaged-by: [blackarch]
blackarch-builddate: "2022-03-19"
build-system: windows
license: MIT
meta-checked: "2026-11-09"
github-repo: "crazy-max/WindowsSpyBlocker"
github-stars: 5238
github-pushed: "2026-10-15"
github-archived: false
github-issues: 47
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 30
gh-activity-checked: "2026-11-09"
---

# windowsspyblocker

> Block Windows telemetry and tracking with tested firewall rule sets, applied through a small console app.

## What it's for

WindowsSpyBlocker (crazy-max) is two things: a maintained collection of firewall, hosts and DNS blocking rules that cut Windows telemetry and data-collection traffic (spy/extra/third-party categories, versioned and testable), and a small Go console application that applies those rule sets to the local Windows firewall through an interactive menu. The rules themselves are maintained as data in the project repository (firewall, hosts and DNS list formats, split into spy/extra categories), so you can also review or reuse the endpoint lists without the application. It is a hardening/privacy tool for machines you administer; it does nothing offensive, and applying its firewall rules affects connectivity to the listed services (including some update/app-store paths), so test before deploying broadly.

## Typical use cases

### Apply blocking rules to the local firewall

The intended everyday use of the application.

```
WindowsSpyBlocker.exe
```

Outcome: on first launch it generates its `app.conf` next to the executable; the console menu offers Telemetry (apply/remove the firewall rule sets and modify NCSI) and Dev (project tools) — choosing Telemetry → Firewall installs the selected spy/extra rules into Windows Firewall, and the menu can also remove them again.

*No corpus evidence — derived from upstream documentation (project README and crazymax.dev docs: application and blocking-rules pages); the packaged distro install ships only the `WindowsSpyBlocker.exe` application.*
