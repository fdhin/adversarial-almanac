---
name: "cms-few"
categories:
  - blackarch-webapp
version: "0.1"
homepage: "http://packetstormsecurity.com/files/64722/cms_few.py.txt.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# cms-few

> 2009-era one-shot CMS fingerprinter and SQL injection path checker for Joomla, Mambo, PHP-Nuke and XOOPS.

## What it's for

cms_few is a single Python 2 script (published on Packet Storm, no repository) that fetches a site's homepage, matches the HTML against the signatures of four CMS families, and — when one matches — offers a numbered menu of hardcoded, years-old component SQL injection URLs to request against the target. It is a historical artifact: the injected components (Joomla com_hwdvideoshare, XOOPS myAds, PHP-Nuke modules) date from the late 2000s, so on modern sites it functions mostly as a fingerprinter. It needs a Python 2 interpreter and takes exactly one argument, the site name; the script prepends `http://` itself.

## Typical use cases

### Identify a legacy CMS and run its injection checks

Point it at a site and walk the interactive menu.

```
cms-few target.example.com
```

Outcome: fetches `http://target.example.com`, reports which of Joomla, Mambo, PHP-Nuke or XOOPS the page appears to run, then prompts for a numeric choice and fires the hardcoded union-select paths for that CMS, printing the responses of any that hit. Exit is automatic if the homepage matches no signature.

No corpus evidence — derived from upstream documentation.
