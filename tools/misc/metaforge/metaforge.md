---
name: "metaforge"
categories:
  - blackarch-misc
version: "115.7b32693"
homepage: "https://github.com/chriswmorris/Metaforge"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: perl
license: GPL3
meta-checked: "2026-11-09"
github-repo: "chriswmorris/Metaforge"
github-stars: 123
github-pushed: "2019-03-18"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# metaforge

> OSINT helper that sweeps a folder of files with exiftool and builds a browsable HTML metadata report.

## What it's for

Metaforge (Chris Morris & Collin Mockbee, a 2019 senior design project) is a small Python 3 application for metadata review during OSINT: point it at a directory of documents and media, and it runs exiftool across everything, stages and filters the extracted metadata, and generates a static HTML report — per-file raw and hexadecimal views, filterable listings, and summary statistics — that you open in a browser. Supported inputs cover common document, image, audio/video and executable types (doc/docx, pdf, html, jpg/png/gif/svg, mp3/mp4/mkv, exe/dll, odp/ods/odt, ppt/pptx, and more); exiftool must be installed. It is menu-driven rather than flag-driven: the whole workflow is an interactive wizard with no command-line options. Note that the distribution package description ("Auto Scanning to SSL Vulnerability") is a miscopy — the tool has nothing to do with SSL.

## Typical use cases

### Build a metadata report for a folder of files

Place the files to review in the application's `media/` directory (for the packaged launcher, `/usr/share/metaforge/media/`), then start the wizard.

```
metaforge
```

Outcome: the wizard runs its steps — dependency check, a prompt for a project name (leave blank for a generated name), the exiftool sweep over `media/`, and filtering — then prints the project path under `User_Projects/`; open `index.html` inside it for the browsable metadata report.

*No corpus evidence — derived from upstream documentation.*
