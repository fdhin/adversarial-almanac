---
name: "python-oletools"
categories:
  - blackarch-binary
version: "0.60.2"
homepage: "https://pypi.org/project/oletools/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: python
license: GPL2
aliases: ["python2-oletools"]
meta-checked: "2026-11-09"
github-repo: "decalage2/oletools"
github-stars: 3446
github-pushed: "2026-02-14"
github-archived: false
github-issues: 522
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# python-oletools

> Python suite for dissecting Microsoft OLE2/OpenXML documents: VBA macro extraction and analysis, embedded-object carving, DDE-link detection, and metadata — the standard first pass on a suspicious Office file.

## What it's for

Use this to triage a potentially malicious Office document (doc/dot/xls/xlsb/ppt … and their OpenXML successors) or RTF: identify what makes it suspicious, extract and deobfuscate any macros, carve embedded payloads, and dump metadata. Each concern is a separate command — `oleid` for triage, `olevba` for VBA, `mraptor` for macro-suspicion scoring, `msodde` for DDE/DDEAUTO links, `rtfobj`/`oleobj` for embedded objects, `olemeta`/`oletimes`/`oledir`/`olemap` for structure. The tools work on OLE Compound Files and OpenXML zips, handle encrypted files with `-p`, and recurse into archives with `-r`. They analyze and extract; they do not emulate the payload or run it.

## Typical use cases

### Triage a suspicious document

Get a risk-oriented overview before deeper analysis.

```
oleid suspicious.doc
```

Outcome: prints indicators (macro presence, encryption, executable content, format) each with a risk level, pointing you at which oletools command to run next.

### Extract and analyze VBA macros

Pull macro source, IOC strings, and suspicious keywords out of a document.

```
olevba suspicious.doc
```

Outcome: lists the VBA modules found with their full source code plus auto-executed macros, IOC indicators (URLs, file paths, registry keys), and obfuscated-string detection such as `Hex`/`Base64`/`StrReverse` calls.

### Scan a directory of samples recursively

Batch-process a whole corpus of documents.

```
olevba -r samples/
```

Outcome: walks `samples/` recursively and analyzes every supported file (also entering zip archives when combined with `-z`), so a mail-attachment drop can be triaged in one run.

### Score macros for malice

Apply the MacroRaptor rules to flag auto-executing, suspicious macros.

```
mraptor suspicious.doc
```

Outcome: prints a verdict per file (`SUSPICIOUS` or `OK`) based on the combination of auto-exec, write/suspicious, and execute behavior found in the macros — well suited to filtering large batches before manual review.

### Carve embedded objects from an OLE file

Extract objects planted inside a document's OLE streams.

```
oleobj -d extracted/ malicious.doc
```

Outcome: finds embedded OLE packages and writes each extracted object (often the dropped payload) into `extracted/` for further inspection.

### Carve objects from an RTF file

Extract `objdata` payloads hidden in RTF.

```
rtfobj document.rtf
```

Outcome: parses the RTF for `\objdata` (and exploit-related) objects, saving each to a file and flagging executable content and known exploit patterns.

### Reveal DDE field links

Extract DDE/DDEAUTO fields used to execute without macros.

```
msodde suspicious.docx
```

Outcome: prints any DDE/DDEAUTO field codes found in the document (the classic macro-free execution trick), optionally JSON-formatted with `-j`.

*No corpus evidence — derived from upstream documentation.*
