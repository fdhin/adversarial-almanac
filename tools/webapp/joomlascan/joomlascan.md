---
name: "joomlascan"
categories:
  - blackarch-webapp
version: "1.2"
homepage: "http://packetstormsecurity.com/files/62126/joomlascan.2.py.txt.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# joomlascan

> 2008-era Python script that probes a Joomla site for known-vulnerable RFI paths and files.

## What it's for

joomlascan (v1.2, by d3hydr8 of darkc0de, distributed via Packet Storm) is a minimal Joomla checker: it sends HEAD/GET requests for a hard-coded list of paths and files that were known remote-file-inclusion vectors in Joomla and its components, and reports the HTTP status each probe returned. Anything that answers with a 200 is a path worth looking at manually. It has no vulnerability database, no version detection, and no component enumeration — those live in its modern siblings in this vault: joomscan (the OWASP scanner, the family's flagship), joomlavs (Ruby component/module/template scanner), jooforce (admin-login brute forcer), and jomplug (component fingerprinter via Packet Storm). It is Python 2 and its path list froze in 2008, so treat it as a quick legacy check, not an assessment.

No corpus evidence — derived from upstream documentation (packaged source).

## Typical use cases

### Probe one Joomla site for vulnerable RFI paths

Run the built-in path list against a target.

```
joomlascan www.example.com
```

Outcome: the tool announces itself, walks its list of known-vulnerable paths and files with HEAD requests, and prints each path together with the HTTP status the server returned — 200 responses mark paths to inspect manually.

### Probe through a proxy, hiding 404 noise

Route the scan and keep the output readable.

```
joomlascan www.example.com -404 -proxy 127.0.0.1:8080
```

Outcome: probes are sent via the specified proxy (tested with a connection check first), and `-404` suppresses the lines for not-found paths so only hits and errors remain.
