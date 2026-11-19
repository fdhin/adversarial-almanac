---
name: "metoscan"
categories:
  - blackarch-webapp
version: "05"
homepage: "http://www.open-labs.org/"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-21"
build-system: make
license: custom
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# metoscan

> Old C utility that reports which HTTP methods a web server supports.

## What it's for

metoscan (version 05, circa 2011, from the now-defunct open-labs.org) probes a web server to determine which HTTP methods it accepts — the OPTIONS/TRACE/PUT-style enumeration that flags dangerous verbs or debug handlers. It is a small C program built against libcurl; the package installs a single `metoscan` binary and a README.txt, and its source is only reachable through a web.archive.org snapshot of the original site. Because the upstream and its documentation are gone, no command syntax can be verified against any authoritative source, so this note is description-only: run the binary with no arguments to print its usage before relying on it. For the same check with a maintained tool, `nmap --script http-methods` or a manual `OPTIONS` request cover the same ground.

No corpus evidence; no verifiable CLI — description-only note (upstream archived, documentation unavailable).
