---
name: "cookie-cadger"
categories:
  - blackarch-fuzzer
version: "1.08"
homepage: "https://cookiecadger.com/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: custom:FreeBSD
meta-checked: "2026-11-09"
github-repo: "CookieCadger/CookieCadger"
github-match: "exact"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 149
github-pushed: "2014-10-16"
github-archived: false
github-issues: 15
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cookie-cadger

> A session-hijacking audit tool that sniffs Wi-Fi or wired Ethernet for plain-HTTP credential leaks.

## What it's for

Cookie Cadger, by Matthew Sullivan (demonstrated at DerbyCon 2012),
demonstrates and detects sidejacking: it captures traffic through tshark,
identifies HTTP GET requests that leak session identifiers or other
sensitive values, and can replay a captured request in a browser so you end
up in the victim's session. Where Firesheep hardcoded a site list, Cookie
Cadger matches requests with updatable detection plugins, works from a live
interface or a saved capture, and logs findings to SQLite or MySQL — which
makes it useful for finding "leaky" endpoints on your own sites that still
emit session data over unencrypted HTTP. It is primarily a Java GUI
application with a headless capture mode; it does not produce a finished
audit report on its own.

## Typical use cases

### Launch the GUI

```
cookie-cadger
```

Outcome: the Java window opens with the capture-interface picker; once
capture starts, intercepted requests appear in the session list, and
selecting one replays it in Firefox under the captured user's identity.
Requires the `tshark` binary on the path.

### Headless capture on one interface

Capture and log leaked requests without the GUI.

```
cookie-cadger --headless=on --interfacenum=2 --tshark=/usr/bin/tshark
```

Outcome: captures on interface number 2 and logs recognized requests to the
local SQLite database; stop with Ctrl+C. Running `--headless=on` without
`--interfacenum` prints the numbered interface list to pick from.

### Capture with session detection and MySQL logging

```
cookie-cadger --headless=on --interfacenum=2 --detection=on --dbengine=mysql --dbhost=localhost --dbuser=user --dbpass=Password1 --dbname=cadgerdata
```

Outcome: same capture, but the session-detection plugins flag requests that
carry login state, and every finding is written to the MySQL database
`cadgerdata` instead of local SQLite, ready for later analysis.

### Demo mode without a capture interface

```
cookie-cadger --demo=on
```

Outcome: replays bundled demo traffic through the tool, so the detection and
replay workflow can be explored without a wireless card in monitor mode.

*No corpus evidence — derived from upstream documentation.*
