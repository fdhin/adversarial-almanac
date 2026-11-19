---
name: "mbenum"
categories:
  - blackarch-windows
version: "1.5.0"
homepage: "http://www.cqure.net/wp/mbenum/"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: windows
license: GPL
meta-checked: "2026-11-09"
github-repo: "Seabreg/mbenum"
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

# mbenum

> Enumerates the service registrations held by a Windows master browser to map a whole environment in one query.

## What it's for

MBEnum (by Patrik Karlsson, distributed from cqure.net) queries the NetBIOS **master browser** — "MB" in the name is the master browser, not the machine's firmware — for everything it has registered. Windows servers and workstations register the services they run (Terminal Services, SQL Server, RAS server, and so on) with the domain's master browser, and MBEnum pivots that browse-list data into an inventory, giving an overall picture of a Windows environment without scanning each host individually.

It is a Windows console tool that reads browse-list registrations only: it is not a port scanner and does not authenticate to targets. Useful in the reconnaissance phase against legacy environments where NetBIOS browsing is still active; on modern networks where browsing is disabled it simply returns nothing.

GitHub: third-party mirror of the cqure.net source (Seabreg/mbenum).

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Inventory registered services by server

```
mbenum -s \\198.51.100.10 -p 1
```

Outcome: queries the master browser on the given host and prints every registered machine with the services it advertises (Terminal Services, SQL Server, RAS, etc.), grouped by server — a one-shot environment overview.

### Group the same data by service

```
mbenum -d EXAMPLE -p 2
```

Outcome: queries the browse list for domain `EXAMPLE` and presents the results grouped by service (presentation mode 2; mode 3 lists services vertically), which makes it easy to spot every host running a particular service such as SQL Server.

### Filter the output to a service of interest

```
mbenum -s \\198.51.100.10 -f SQL -p 1
```

Outcome: restricts the by-server listing to entries matching the filter, so only the machines registering matching services (for example the SQL Server instances) are shown.
