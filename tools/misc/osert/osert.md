---
name: "osert"
categories:
  - blackarch-misc
version: "100.4eef9b9"
homepage: "https://github.com/noraj/OSCP-Exam-Report-Template-Markdown"
packaged-by: [blackarch]
blackarch-builddate: "2026-08-24"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "noraj/OSCP-Exam-Report-Template-Markdown"
github-stars: 4220
github-pushed: "2026-08-12"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# osert

> Markdown report templates plus a generation script for Offensive Security certification exam reports.

## What it's for

osert ships Markdown report templates for Offensive Security exams (OSCP, OSWE, OSEP, OSED, OSWA, OSDA and more) together with a small Ruby script that renders them. You write the report in Markdown; Pandoc with the Eisvogel LaTeX template produces the PDF in the required submission format, and the script also builds the 7z archive and prints the MD5 sum the submission process expects. It exists so the report-writing window is spent on content, not on Word/LibreOffice formatting. It is a documentation toolchain, not a scanning or exploitation tool. On BlackArch the script is installed as `osert`; from a source checkout run it as `ruby osert.rb`.

## Typical use cases

### Start a report from a template

Pick the exam template to write against.

```
osert init
```

Outcome: interactive prompts to choose the exam template and a syntax-highlight style, then copies the template into your working directory for editing.

### Generate the submission PDF and archive

Render the finished report into everything the submission needs.

```
osert generate
```

Outcome: runs Pandoc to produce the PDF (with optional preview), packs the 7z archive, and prints the MD5 hash to verify after uploading.

### Render the PDF manually with Pandoc

Skip the script and drive Pandoc directly for full control over options.

```
pandoc src/OSCP-exam-report-template_whoisflynn_v3.2.md -o output/OSCP-OS-XXXXX-Exam-Report.pdf --from markdown+yaml_metadata_block+raw_html --template eisvogel --table-of-contents --toc-depth 6 --number-sections --top-level-division=chapter --highlight-style breezedark --resource-path=.:src
```

Outcome: writes `output/OSCP-OS-XXXXX-Exam-Report.pdf` from the template using the Eisvogel LaTeX template, with table of contents, numbered chapters and syntax highlighting — the same render the script performs.

*No corpus evidence — derived from upstream documentation.*
