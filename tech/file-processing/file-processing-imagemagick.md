---
technology: "ImageMagick"
kind: bug-bounty-methodology
audience: ai-agent
family: web-platforms
role: implementation-variant
layer: image conversion/manipulation suite (convert, magick, identify; coders, delegates, policy.xml)
homepage: "https://imagemagick.org"
github-repo: "ImageMagick/ImageMagick"
status: confirmed
provenance: "corpus-grounded (strict gate 2026-11-12: 8 writeup files match markers, target-side attack chains confirmed in 5 — PNG tEXt profile file-read plus chained binwalk escalation, -authenticate shell injection, AppImage delegate RCE via scheduled identify, PHP Imagick object-instantiation abuse, convert-shell construction injection; one more used convert purely as attacker-side crafting) + upstream-verified (CVE-2022-44268, CVE-2020-29599 CVE record, CVE-2024-41817 NVD/GitHub advisory; note CVE-2022-4510 belongs to binwalk, not ImageMagick — see ledger). Companion area router: [[file-processing|File-processing services]]. Enrichment pass 2 new-unit wave A, 2026-11-12."
---

# ImageMagick — implementation methodology

Companion router: [[file-processing|File-processing services area router]]. This
page covers ImageMagick as a target surface: the PNG profile file-read, the
`-authenticate` shell injection, policy/delegate configuration abuse, the AppImage
RCE, and the two ways web apps hand it command lines.

Target model: `convert` / `magick` / `identify` on the host (7.x "magick", 6.x
"convert"), config at `/etc/ImageMagick-*/` (`policy.xml`, `delegates.xml`),
sometimes reached through PHP's Imagick binding or an image-processing web feature.
Ghostscript presence doubles the surface.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| CVE-2022-44268 PNG tEXt read | ImageMagick ≤ 7.1.0-49 era (before 7.1.0-52 fix band), PNG processing | upload | Arbitrary file read into output image — High |
| CVE-2020-29599 `-authenticate` | ImageMagick < 6.9.11-40 / 7.0.10-40, PDF coder live | upload | Shell injection via PDF password — Critical |
| CVE-2024-41817 AppImage delegate | AppImage builds ≤ 7.1.1-35 | file landing local | RCE as parser user — Critical |
| policy.xml bypass | policy denies coder but path/delegate leaks | varies | Route around restrictions — Medium |
| convert-in-shell string building | app builds `convert {input} ...` with your path | upload | Command injection via filename — Critical |
| Imagick binding object instantiation | PHP app passes user input to Imagick ctor | upload | Instantiation gadgets → SSRF/file read — High |

## Phase 0 — Fingerprint

- Web side: any resize/crop/thumbnail/convert feature; derivative images prove the
  pipeline. PHP gallery apps that name Imagick are binding-surface candidates.
- Host side after foothold: `magick --version` / `convert -version`, then read
  `/etc/ImageMagick-*/policy.xml` and `delegates.xml` — they tell you which coders
  are denied and which delegates will run. A lone `magick` binary in an app
  directory (not distro-packaged) hints at an AppImage-style bundle — jump to
  Phase 3.
- Version gate: 7.1.0-49 and below → 44268 live; < 6.9.11-40 / 7.0.10-40 → 29599
  live.

## Phase 1 — CVE-2022-44268: PNG `tEXt` arbitrary file read

A crafted PNG carries a `tEXt` chunk whose value is the literal string `profile`
with a `rawprofile` type naming a target file; when ImageMagick processes the PNG it
reads that local file into the image's metadata, and the *output* image you retrieve
carries the file's bytes (hex-encoded in its profile data).

```bash
# craft: PNG with tEXt chunk -> (type rawprofile; value "profile")
# plus a second chunk naming the file: e.g. /etc/passwd or /home/user/.ssh/id_rsa
python3 png_exploit.py 198.51.100.10 /etc/passwd   # public PoC shape
# upload -> trigger conversion -> download the produced image
exiftool output.png        # or binwalk/strings: the file content rides inside
```

Observed chain: upload image → service converts it (proving ImageMagick) → read
`/etc/passwd`, service configs, SSH keys → credentials → host. On one deployment the
same box's image pipeline *also* ran binwalk over uploads on a schedule — its PFS
extraction path traversal (CVE-2022-4510, a **binwalk** CVE, not ImageMagick —
attribution checked) converted the file-read foothold into root. Expect image
pipelines to be stacked; enumerate every binary that touches uploads.

## Phase 2 — CVE-2020-29599: `-authenticate` PDF password shell injection

Upstream-verified: ImageMagick before 6.9.11-40 and 7.0.10-40 mishandles the
`-authenticate` option (the password for protected PDFs) — the user-controlled
password is not escaped before the PDF coder (`coders/pdf.c`) uses it, so shell
metacharacters in the password execute commands when a protected PDF is converted.
The attack needs the target to convert a PDF *you* supply the password for — a
"convert this protected document" or preview feature. Deliver a password-protected
PDF whose password is the payload string (public PoCs exist); Ghostscript executes
on the other side of the coder. Observed as the second stage on a box already
holding a parser foothold — same upload endpoint, different file type.

## Phase 3 — CVE-2024-41817: AppImage builds and delegate RCE

The **AppImage** distribution of ImageMagick (≤ 7.1.1-35) mishandles its
environment/config loading — a crafted file (malicious `.mgk`/delegation XML
arriving via the image's directory) leads to arbitrary code execution when
ImageMagick processes input. Field shape: a scheduled `identify`/`magick` walk over
an uploads directory (an `identify_images.sh` cron), root-owned, ImageMagick shipped
as a bundled binary — place the crafted file, wait for the sweep, receive root.
Gate it on the build type: distro packages are not AppImages; check the binary's
path and packaging before claiming the bug.

## Phase 4 — App-layer invocation bugs (no ImageMagick CVE needed)

Two recurring application shapes that hand your input to ImageMagick's command line
or API:

- **Shell string building**: `command = f"{CONVERT_PATH} {original_path} -crop ..."`,
  with `original_path` derived from your upload name or a URL parameter — inject
  via the classic channels (backticks, `$(...)`, quotes) straight into the shell.
  Observed on an image-editing endpoint that fed a gallery app.
- **PHP Imagick instantiation**: an endpoint passes user input into an Imagick
  constructor — arbitrary object instantiation gadgets (instantiating Imagick on
  attacker-chosen strings can become SSRF or file-read primitives; code execution
  when gadget chains apply). Observed on a PHP gallery with an effects feature.

## Phase 5 — Policy and delegate configuration (post-foothold reading)

- `policy.xml` is the intended brake (`<policy domain="coder" rights="none"
  pattern="{PS,PDF,EPS,...}"/>` shapes). Read it to learn what the operator *tried*
  to block — then look for the gap: case mismatches, missing coders (MSL, EPHEMERAL,
  HTTPS), delegates not covered by the policy. Policy bypass classes have their own
  CVE history; treat the config as a map of denied doors, not a wall.
- `delegates.xml` names every helper binary ImageMagick shells out to (Ghostscript,
  ffmpeg, curl…) — each delegate line is a candidate sink for the content-channel
  injections above.
- Attacker-side note: when *your* convert refuses ("not authorized" on PDF/PS),
  your local policy.xml is the reason — the fix is your own config, not more
  payload gymnastics.

## Command quick reference

```bash
magick --version                       # or convert -version — gate the CVEs
identify -verbose crafted.png          # post-read extraction (profile data)
cat /etc/ImageMagick-*/policy.xml      # denied coders map
cat /etc/ImageMagick-*/delegates.xml   # shellout sinks
# attacker-side crafting when needed:
convert -size 595x842 xc:white dummy.pdf   # harmless PDF for pipeline probing
```

## Tool mapping (prose)

convert/magick/identify for both target-side confirmation and attacker-side
crafting; python with public PoC scripts for the PNG tEXt exploit; exiftool to
extract the exfiltrated profile bytes from output images; binwalk when the
deployment stacks an extraction step. Tools vault owns per-tool detail.
