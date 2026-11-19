---
name: "xsss"
categories:
  - blackarch-webapp
version: "0.40b"
homepage: "http://www.sven.de/xsss/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# xsss

> Brute-force Perl XSS scanner that crawls a target and feeds HTML control characters into query strings and forms.

## What it's for

xsss (by Sven Neuhaus, version 0.40b, released 2005) is a classic brute-force cross-site scripting scanner: it crawls the target with WWW::Mechanize, submits special data containing HTML control characters into URL query strings and — on request — form fields, and flags every response that echoes the injected characters back. It is deliberately simple and request-limited, aimed at quick triage of a site rather than deep auditing, and it cannot (yet) inject into select options, checkboxes or radio buttons due to HTML::Form restrictions. The upstream is the author's page at www.sven.de/xsss; the tool ships as a single Perl script installed as `xsss`.

## Typical use cases

### Scan a site with default recursion

```
xsss http://target.example.com/
```

Outcome: xsss crawls up to 5 levels deep, garbling query strings with HTML control characters, and finishes with a summary line `Done. Found N suspects in M requests`.

### Include form submission testing

```
xsss --forms http://target.example.com/
```

Outcome: in addition to query strings, HTML forms on discovered pages are submitted with the special data (form scanning is intrusive, so it is off by default).

### Scan a list of URLs with bounded requests

```
xsss --list=urls.txt --noqueries --maxcount=500
```

Outcome: reads one URL per line from `urls.txt`, disables query-string fuzzing (pair with `--forms` for form-only testing), and stops after 500 requests; each suspect reflection is reported as it is found.

*No corpus evidence — derived from upstream documentation (README and usage() of the packaged 0.40b tarball from www.sven.de).*
