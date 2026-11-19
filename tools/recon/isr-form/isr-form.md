---
name: "isr-form"
categories:
  - blackarch-recon
version: "1.0"
homepage: "http://www.infobyte.com.ar/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# isr-form

> Perl parser that walks a mirrored web site and reports every HTML form — action, method and fields — for attack-surface analysis.

## What it's for

Cataloguing the form-backed input surface of a web application: once you have mirrored a site (its `.html`, `.htm`, `.asp` and `.php` pages), ISR-form.pl parses every file, extracts the `<form>` tags and their inputs, and generates a consolidated report. Written by Francisco Amato of Infobyte Security Research (ISR). It is purely an offline parser of already-downloaded content — it does not fetch anything itself, does not submit forms, and does not understand JavaScript-injected forms, so mirror the rendered pages first. Useful as a pre-audit checklist of every parameter a target exposes.

## Typical use cases

### Inventory every form on a mirrored site

```
wget -r -l 3 http://target.example.com
ISR-form.pl -l ./target.example.com -o target.example.com.forms.txt
```

Outcome: the `wget` mirror populates `./target.example.com` with the site's pages; ISR-form.pl then walks that directory (`-l`, full path to the mirror), parses each form-bearing file and writes the report to `-o` — a per-page listing of forms with their actions, methods and fields. Review the report to enumerate each injectable parameter before hands-on testing.

*No corpus evidence — derived from the packaged upstream source and its README (ISR-form v1.0, Infobyte Security Research).*
