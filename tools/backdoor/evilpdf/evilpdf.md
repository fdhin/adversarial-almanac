---
name: "evilpdf"
categories:
  - blackarch-backdoor
version: "5.43696a8"
homepage: "https://github.com/thelinuxchoice/evilpdf"
packaged-by: [blackarch]
blackarch-builddate: "2020-06-15"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "thelinuxchoice/evilpdf"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# evilpdf

> Smuggles an executable inside an ordinary-looking PDF document (PyPDF2), so a payload travels as a trusted file type.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Delivery-stage social engineering. evilpdf hides an executable file within a PDF carrier so the document passes casual inspection and content handling that trusts the PDF type. It is a small interactive Python script (`evilpdf.py`) by thelinuxchoice; the original GitHub repository is gone, but BlackArch still packages the pinned commit. It only conceals the payload — the recipient still has to extract and run the embedded executable, so it works only as part of a delivery story that gets them to do so, on systems you are authorized to test.

## Typical use cases

### Embed an executable in a carrier PDF

```
python evilpdf.py
```

Outcome: interactive prompts walk you through choosing the executable and the carrier document, then write an output PDF with the payload embedded — ready to use as the delivery artifact in an authorized phishing simulation.
