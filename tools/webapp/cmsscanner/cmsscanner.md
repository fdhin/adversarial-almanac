---
name: "cmsscanner"
categories:
  - blackarch-webapp
version: "0.15.0.r1.g65de34e"
homepage: "https://github.com/wpscanteam/CMSScanner"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-05-31"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "wpscanteam/CMSScanner"
github-stars: 133
github-pushed: "2026-05-15"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# cmsscanner

> Ruby framework gem for building CMS scanners — the engine underneath WPScan.

## What it's for

CMSScanner is not an end-user scanner: it is an experimental Ruby gem (class `CMSScan::Scan`) from the WPScan team that provides the plumbing a website scanner needs — target handling, HTTP client, caching, formatters and a controller model — so a new scanner is mostly new controllers and finders. WPScan itself is built on it. The gem ships a minimal example app that registers one example controller and accepts a `--url`; the packaged `cmsscanner` command runs exactly that example. Reach for this when you are writing a scanner for another CMS in Ruby, or want to see how WPScan's core behaves; for actually auditing a WordPress site, use WPScan directly.

## Typical use cases

### Run the example scanner

Exercise the framework end-to-end through its bundled example app.

```
cmsscanner --url https://target.example.com
```

Outcome: runs the example scan application (via the gem's `example/` tree) against the URL — proof the framework's option parsing, request layer and output formatting work; the example controller's findings print in the chosen format.

### Use the framework from Ruby

Drive the scan engine programmatically.

```
ruby -r cmsscan -e 'CMSScan::Scan.new { |s| s.controllers << CMSScan::Controller::Example.new; s.run }'
```

Outcome: the same flow as the example binary expressed as Ruby — register your own controllers in the block instead of `Controller::Example`, and `s.run` parses options and executes them; this is the extension point a new CMS scanner is built on.

No corpus evidence — derived from upstream documentation.
