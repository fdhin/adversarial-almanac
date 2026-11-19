---
name: "pdfwalker"
categories:
  - blackarch-misc
version: "7.64c17f0"
homepage: "https://github.com/gdelugre/pdfwalker"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: ruby
license: GPL3
meta-checked: "2026-11-09"
github-repo: "gdelugre/pdfwalker"
github-stars: 39
github-pushed: "2017-10-07"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pdfwalker

> GTK application for walking through the internal object tree of a PDF document.

## What it's for

PDF Walker is a graphical frontend to the Origami PDF framework. It opens a document and lets you browse its object graph the way a hex editor browses bytes: the object tree including packed object streams, cross-references, page contents and form fields. You can follow references interactively, search objects with regular expressions, decode and extract streams, and even sign documents. Use it when dissecting a malformed or suspicious PDF by hand. It is purely interactive — the command line accepts only an optional file to open (plus `-v`/`-h`); there is no scripting or batch-analysis surface, and it does not automate vulnerability detection.

## Typical use cases

### Open a PDF in the object browser

Load a document straight into the graphical explorer.

```
pdfwalker document.pdf
```

Outcome: opens the GTK window with the document's object tree loaded — click objects to inspect them, follow references, regex-search object bodies, and dump or decode streams through the GUI.

*No corpus evidence — derived from upstream documentation.*
