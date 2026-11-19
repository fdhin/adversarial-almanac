---
name: "quickrecon"
categories:
  - blackarch-recon
version: "0.3.2"
homepage: "http://packetstormsecurity.com/files/104314/QuickRecon.3.2.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Seabreg/quickrecon"
github-stars: 0
github-pushed: "2019-05-19"
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

# quickrecon

> PyQt4 desktop GUI for first-pass information gathering: subdomain bruteforce, zone transfers, search-engine email harvesting and Shodan host lookups.

## What it's for

QuickRecon is a small Python 2 / PyQt4 desktop application written by Filip Szymański for simple, GUI-driven reconnaissance of a domain: bruteforcing subdomain names, attempting DNS zone transfers, harvesting email addresses from Google, Google Groups and Bing, finding human relationships via XHTML Friends Network (XFN) markup, and checking a host in the Shodan database (requires a Shodan API key, stored in `shodan_key.cfg` or entered in the GUI). It is entirely menu-driven — there is no command-line interface and no batch mode. The project dates from 2011, its original Google Code home is gone, and it is now distributed as a PacketStorm tarball that distro recipes repackage; expect Python 2 era dependencies (PyQt4, BeautifulSoup, dnspython) and treat it as a legacy/throwback tool rather than a daily driver.

## Typical use cases

### Launch the GUI and run a recon sweep

Start the application from the packaged launcher (or run `python2 quickrecon_gui.pyw` inside the extracted source tree).

```
quickrecon
```

Outcome: opens the PyQt4 main window with one tab per gathering mode — subdomain bruteforce, zone transfer, email harvesting, XFN relationship discovery and Shodan host lookup. Enter a domain and Shodan key (for the Shodan tab) in the relevant tab and the results are displayed in the GUI; there is no file or stdout report.

*No corpus evidence — derived from upstream documentation (author README in the packaged 0.3.2 tarball).*

GitHub: third-party mirror of Filip Szymański's original QuickRecon (Seabreg/quickrecon, v0.3.2 — the packaged version, README and PyQt4 GUI sources intact).
