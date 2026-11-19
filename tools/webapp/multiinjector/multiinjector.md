---
name: "multiinjector"
categories:
  - blackarch-webapp
version: "0.4"
homepage: "http://chaptersinwebsecurity.blogspot.de/2008/11/multiinjector-v03-released.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# multiinjector

> Batch SQL-injection utility driven by a list of parameterized URLs.

## What it's for

MultiInjector (Raviv Raz, 2008-2011, python2 + pycurl) takes a file of URI addresses, keeps the ones with query parameters, and tests each parameter for SQL injection; for every vulnerable parameter it then performs a signature-evasive injection chosen from an interactive menu: mass defacement via a cursor-based `UPDATE` across all text columns, an OS command through `xp_cmdshell`, or an arbitrary SQL query. It is a product of its era — the built-in payloads are MSSQL-oriented and evasive against the IDS signatures of the late 2000s — so treat it as a legacy batch injector rather than a modern SQLi platform (sqlmap covers the same ground today with maintained parsing).

## Typical use cases

### Batch-test a URL list

```
multiinjector urls.txt
```

Outcome: reads the file, filters to parameterized URLs, then presents the interactive menu (goal: defacement / OS command / SQL query, plus the command or query text, connection count, and optional proxy); after the choice it fires the injection at every vulnerable parameter found and reports per-URL results. Python 2 is required at runtime.

No corpus evidence — derived from the packaged source (`MultiInjector.py` v0.35 usage and menu).
