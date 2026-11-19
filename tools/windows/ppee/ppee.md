---
name: "ppee"
categories:
  - blackarch-windows
version: "1.12"
homepage: "https://www.mzrst.com/"
packaged-by: [blackarch]
blackarch-builddate: "2019-01-26"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/PPEE"
github-stars: 11
github-pushed: "2019-03-24"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ppee

> GUI PE explorer and editor for reversing and malware triage, with malware-repository lookups and YARA scanning built in.

## What it's for

PPEE ("puppy") is a free Windows GUI for statically inspecting 32- and 64-bit PE files: every data directory — export, import, resource, exception, certificate, base relocation, debug, TLS, load config, bound import, IAT, delay import, CLR (.NET) and Rich header — is laid out in a tree view, and most fields can be edited in place. It is deliberately robust against malformed and crafted PE files, which makes it handy for malware analysis where parsers routinely break. Two companion plugins ship with it: FileInfo, which computes hashes (MD5/SHA1/SHA256, ImpHash, ssdeep fuzzy hash), entropy and version info, and queries the file against public malware repositories without uploading it; and YaraPlugin, which tests YARA rules against the opened file. It is a GUI application with no scriptable CLI — automated batch triage belongs to command-line tooling. The packaged version is 1.12; the project site carries newer releases, including a cross-platform rewrite in preview.

## Typical use cases

### Inspect a suspect PE file's full structure

Launch the GUI and load the binary under review.

```
PPEE.exe
```

Outcome: opens the PE explorer; once a file is loaded, all data directories appear in a navigable tree with sections, imports (ordinals resolved to names), resources, the .NET metadata and the Rich header, plus anomaly highlighting and a suspicious-strings view (customizable via Suspicious.txt). Use the FileInfo plugin panel for hashes/entropy and repository verdicts, and YaraPlugin to match rules against the file.

*No corpus evidence — derived from upstream documentation.*
