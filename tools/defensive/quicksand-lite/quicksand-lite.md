---
name: "quicksand-lite"
categories:
  - blackarch-defensive
version: "33.c3edf92"
homepage: "https://github.com/tylabs/quicksand_lite"
packaged-by: [blackarch]
blackarch-builddate: "2023-10-08"
build-system: other
license: MPL2
meta-checked: "2026-11-09"
github-repo: "tylabs/quicksand_lite"
github-stars: 127
github-pushed: "2023-09-23"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# quicksand-lite

> Scans Microsoft Office and email documents for exploit streams and hidden obfuscated executables.

*No corpus evidence — derived from upstream documentation.*

## What it's for

QuickSand Lite (by Tylabs) is a compact C command-line scanner for triaging suspected malicious documents: doc/docx/docm, rtf, ppt/pptx, xls/xlsx, MIME/MSO and .eml files, plus PDF when looking for executables. It decodes embedded streams (OpenXML unzip, hex, base64, zlib, MIME/MSO XML, RTF preprocessing), applies a key-dictionary XOR search up to 256 bytes plus ROL/ROR/NOT ciphers, and matches decoded content against bundled YARA signatures (`quicksand_exe.yara`, `quicksand_exploits.yara` for CVE/active-content, `quicksand_general.yara` for malware families) so it can flag unknown or obfuscated exploits and extract the embedded Windows/macOS/Linux executables or VBA they hide. It is the first open-source iteration of the QuickSand framework and is no longer actively developed; the maintained Python version 2 lives at quicksand.io. It is a document triage tool, not an antivirus or a sandbox. Upstream's `build.sh` produces `./quicksand.out`; distro packages typically install it as `quicksand`. YARA rules are located via the `QS` environment variable when the binary is run outside its source directory.

## Typical use cases

### Triage a suspicious document

Get a fast verdict on whether an office document contains exploit-like streams or embedded executables.

```
quicksand.out suspect.rtf
```

Outcome: prints a per-stream report naming detected objects and any YARA matches (executables, CVE/exploit signatures, active content, malware families), letting you decide whether to escalate the sample.

### Produce a JSON report

Capture the same analysis as JSON for pipelines and ticketing.

```
quicksand.out -j suspect.doc > quicksand-report.json
```

Outcome: emits the stream/object/detection findings as a JSON document to the redirected file instead of plain-text output.

### Extract embedded executables and objects

Pull the payload out for further analysis.

```
quicksand.out -d -o -p extracted/ suspect.doc
```

Outcome: drops recovered executables (`-d`) and all other stream objects (`-o`) into the directory given with `-p`, so the payload can be passed to a disassembler or YARA workflow.

### Widen the cipher search

When the plain XOR dictionary finds nothing but the file still looks hostile, try additional obfuscation families.

```
quicksand.out -m -n suspect.doc
```

Outcome: additionally attempts arithmetic (`-m`) and bitwise-NOT (`-n`) ciphers over the streams before matching; expect noticeably longer runtime.

### Batch a directory of samples

Sweep a whole triage folder in one run.

```
quicksand.out samples/
```

Outcome: iterates every file in the directory and prints an analysis section per sample, stopping at the first malformed input only if decoding fails hard.
