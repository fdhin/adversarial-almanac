---
name: "backdoorppt"
categories:
  - blackarch-backdoor
version: "88.d0e7f91"
homepage: "https://github.com/r00t-3xp10it/backdoorppt"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-28"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "r00t-3xp10it/backdoorppt"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 472
github-pushed: "2019-12-08"
github-archived: false
github-issues: 4
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# backdoorppt

> Disguise a Windows payload .exe as a Word document (.ppt) via icon swap and extension spoofing.

## What it's for

backdoorppt is a social-engineering prop builder: it takes an existing Windows executable and makes it render as a Microsoft Word document on the target — a Word icon is stamped onto the binary (ResourceHacker running under wine) and the filename is manipulated so the extension reads `.ppt` instead of `.exe`. It supports two spoofing methods, "Right to Left Override" and "Hide Extensions for Known File Types", selected in its `settings` file. It does not build real Office documents, does not change what the executable does, and only accepts Windows binaries as input; the icon choice is limited to the handful of `.ico` files shipped with it.

## Typical use cases

### Run the disguise wizard

Convert a payload into a document-lookalike interactively.

```
backdoorppt
```

Outcome: the script prompts for the payload `.exe` and an icon, stamps a Word icon onto the binary and rewrites the filename with the configured spoofing method; the output file looks like a `.ppt` document on systems where known extensions are hidden, but executes as the original payload.

### Choose the extension-spoofing method

Switch between the two supported filename tricks before generating.

```
nano settings
```

Outcome: editing the `settings` file in the tool directory selects either "Right to Left Override" (Unicode RTL character flips the displayed name) or "Hide Extensions for Known File Types" (appends a spoofed `.ppt` suffix); the next run uses the chosen method.

*No corpus evidence — derived from upstream documentation.*
