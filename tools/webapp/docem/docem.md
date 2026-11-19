---
name: "docem"
categories:
  - blackarch-webapp
version: "21.59db436"
homepage: "https://github.com/whitel1st/docem"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "whitel1st/docem"
github-stars: 691
github-pushed: "2024-01-28"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# docem

> Embeds XXE and XSS payloads into OOXML documents (docx, odt, pptx) at scale.

## What it's for

Docem generates malicious documents for testing file-upload and document-processing pipelines: docx, odt, pptx — any format that is a zip archive of XML files. You mark a sample document with the magic symbol XXCb8bBA9XX at every position a payload should go; docem replaces the markers with payloads from your payload file and writes the generated documents under ./tmp/. Three payload modes control granularity: per_document (each payload embedded across the whole document), per_file (per internal XML file) and per_place (one document per marker occurrence). A batch-capable successor to oxml_xxe; the package ships marked samples and payload files alongside the wrapper.

## Typical use cases

### Generate XXE documents one payload per document

```
docem -s samples/marked/docx_sample_oxml_xxe_mod0/ -pt xxe -pf payloads/xxe_special_6.txt -pm per_document -sx docx
```

Outcome: embeds each XXE payload from the payload file across every marked position of each docx in the sample directory and writes one generated document per payload under ./tmp/.

### Generate one document per internal XML file

```
docem -s samples/marked/docx_sample_oxml_xxe_mod1/ -pt xxe -pf payloads/xxe_special_1.txt -pm per_file -sx docx
```

Outcome: finer-grained than per_document — every payload/file combination inside the archive becomes its own document, isolating which internal XML file carries the payload.

### Generate XSS variants per placement

```
docem -s samples/marked/sample_oxml_xxe_mod1.docx -pt xss -pf payloads/xss_tiny.txt -pm per_place
```

Outcome: for every payload and every marker occurrence in the single sample, writes a separate document under ./tmp/ — the maximum-coverage variant set for finding which embedded position executes.

### Use your own marked sample

```
docem -s my_sample.docx -pt xxe -pf my_payloads.txt -pm per_place
```

Outcome: runs the same generation against a document you marked yourself (insert XXCb8bBA9XX at the positions to fuzz) — the workflow for a specific target document or format.

_No corpus evidence — derived from upstream documentation (README usage; the package ships samples/ and payloads/ next to the wrapper, so the bundled paths above resolve on install)._
