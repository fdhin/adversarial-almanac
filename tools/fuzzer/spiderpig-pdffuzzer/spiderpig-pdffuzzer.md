---
name: "spiderpig-pdffuzzer"
categories:
  - blackarch-fuzzer
version: "0.1"
homepage: "https://code.google.com/p/spiderpig-pdffuzzer/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "Seabreg/spiderpig"
github-stars: 0
github-pushed: "2019-09-14"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# spiderpig-pdffuzzer

> SpiderPig — generates PDF documents that call PDF-reader JavaScript API methods with hostile argument combinations.

*GitHub home: https://github.com/Seabreg/spiderpig — third-party mirror of the code.google.com spiderpig release, verified byte-identical to the packaged tarball.*

*No corpus evidence — derived from upstream documentation.*

## What it's for

SpiderPig (by consoul/dgfish3r, 2010) is a Python fuzzer that targets the JavaScript engine of PDF readers rather than the PDF file format: you list the Acrobat JavaScript API methods you want exercised in a prototype file — one per line, e.g. `app.alert(nIcon,nType,cTitle,oDoc,oCheckbox)` — and SpiderPig generates one PDF per method whose embedded script calls it with combinations of hostile arguments: multi-kilobyte URLs, giant `A` runs, format-string packs, boundary integers (`0x7fffffff`, `INT_MAX+1`, denormals), and `null`/`this`/`new Object()`. Parameter types are inferred from the first letter of each parameter name (n = number, b = boolean, o = object, c = string). Every generated call logs through `console.println`, so when the reader breaks in your debugger you can see exactly which method and argument were executing — the same brute-force approach AxMan and Dranzer applied to ActiveX, applied to the Acrobat JS API.

## Typical use cases

### Generate a corpus from the example prototype file

Builds one PDF per listed JavaScript method.

```
spiderpig pdfs-out test.proto
```

Outcome: reads `test.proto` (the shipped example lists `app.alert`, `this.addIcon`, `util.crackURL`, `this.media.newPlayer`, `app.getNthPlugInName`, ...) and writes one PDF per line — e.g. `app.alert.pdf` — into `pdfs-out/`; open them in the target reader attached to a debugger and follow the console log to the crashing call.

### Fuzz a chosen set of methods

Targets only the API surface you care about.

```
printf 'app.alert(nIcon,nType,cTitle)\napp.beep(nType)\n' > my.proto
spiderpig pdfs-out my.proto
```

Outcome: writes exactly two documents, `app.alert.pdf` and `app.beep.pdf`, each cycling that method's parameters through the built-in evil-value lists.
