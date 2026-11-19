---
name: "directorytraversalscan"
categories:
  - blackarch-windows
version: "1.0.1.0"
homepage: "http://sourceforge.net/projects/httpdirscan/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/DirectoryTraversalScan"
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

# directorytraversalscan

> Windows GUI scanner for directory traversal flaws in web servers and applications.

## What it's for

HTTP Directory Traversal Scanner (John Leitch / autosectools, 2011, .NET/WPF) probes an HTTP server or web application with traversal sequences and compares the responses for evidence that files outside the web root can be read. It is a desktop tool with no command-line interface: you configure the target and scan options in the window and inspect each request's response code, status and body in the built-in viewer. It predates modern CLI web fuzzers and is of interest mainly for legacy engagements; the packaged zip ships the original 1.0.1.0 GUI executable together with its full C# source.

## Typical use cases

### Launch the scanner GUI

Open the tool against a lab web target.

```
wine /usr/share/windows/directorytraversalscan/DirectoryTraversalScan.exe
```

Outcome: the scanner window opens with Host, Port, Path and File fields plus its traversal-payload settings. Enter a test target, start the scan, and it fires the traversal sequences, listing each request with its response code and status so you can select a response and read its body to decide whether filesystem content was returned.

*No corpus evidence — derived from upstream documentation.*
