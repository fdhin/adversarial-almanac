---
name: "stig-viewer"
categories:
  - blackarch-scanner
version: "2.8"
homepage: "https://public.cyber.mil/stigs/stig-viewing-tools/"
packaged-by: [blackarch]
blackarch-builddate: "2019-02-08"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# stig-viewer

> DISA's official Java viewer for STIG and SRG security checklists.

## What it's for

STIG Viewer is the Java desktop application DISA distributes as the U_STIGViewer
jar (from DISA's public.cyber.mil STIG site) for working with Security Technical
Implementation Guides: import compiled STIG and SRG checklist files in XCCDF
format, step through the requirements, record review status, findings and
comments per rule, and save or export completed checklists. It is the
review-and-annotate half of a SCAP compliance workflow — run a SCAP validation
scanner to produce results, then review and document them here. It needs a
Java runtime; the packaged 2.8 release is wrapped as the `stig-viewer`
command.

## Typical use cases

### Open the checklist viewer

Launch the GUI to work through STIG checklists.

```
stig-viewer
```

Outcome: the Java GUI starts (the wrapper runs DISA's STIGViewer jar); from
the menus you import a STIG or SRG checklist archive, review rules, record
findings, and save the completed checklist — saving is manual, the viewer
does not auto-save.

*No corpus evidence — derived from the packaging source (BlackArch PKGBUILD
wrapping DISA's U_STIGViewer 2.8 jar) and DISA's STIG viewing-tools
distribution.*
