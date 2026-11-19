---
name: "androrat"
categories:
  - scanner
version: "git (2023-04-12)"
homepage: "https://github.com/wszf/androrat"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: make
meta-checked: "2026-11-09"
github-repo: "wszf/androrat"
github-stars: 2060
github-pushed: "2023-04-12"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 53
---

# androrat

> Classic Android remote administration tool: a Java/Swing server controlling an APK client that phones home over TCP.

## What it's for

AndroRAT ("Android RAT") is a client/server project from a university team, maintained
here as a fork ("Silent Edition"). The client is an Android service that starts at boot
and connects back to the operator's server — optionally only after a trigger such as an
SMS or a call — while the server is a cross-platform Java/Swing GUI listing connected
clients with per-client tabs for contacts, call logs, messages, GPS/network location, live
SMS and phone-state monitoring, camera pictures and microphone streaming, plus remote
actions like sending SMS, placing calls, opening a URL or vibrating the phone. Deployment
is a build-it-yourself workflow rather than a CLI: hardcode the listener IP and port in
`src/Androrat/src/my/app/client/LauncherActivity.java`, compile the APK with the Android
SDK/Eclipse, and run the Swing server from source; an experimental activity-based client
adds video streaming. There is no documented command-line interface, so this note is
description-only. The project is effectively dormant (last push 2023) and carries no
license file — the original code is a 2010s university artifact useful today mainly as a
reference Android implant architecture.

*No corpus evidence — description-only note (no documented CLI); derived from the upstream README and repository layout.*
