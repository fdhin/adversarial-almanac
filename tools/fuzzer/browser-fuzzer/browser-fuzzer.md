---
name: "browser-fuzzer"
categories:
  - blackarch-fuzzer
version: "3"
homepage: "http://www.krakowlabs.com/dev.html"
packaged-by: [blackarch]
blackarch-builddate: "2017-01-14"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# browser-fuzzer

> Krakow Labs Browser Fuzzer 3 (bf3) — generates numbered CSS/DOM/HTML/JS/XML test cases into a web-served directory and steps a browser through them via auto-refresh.

## What it's for

bf3 (Jeremy Brown, Krakow Labs, 2010) is a standalone C fuzzer for web browsers covering five modules — CSS, DOM, HTML, JavaScript and XML — through two generation engines: a built-in "fuzzing oracle" of curated inputs and a random data generator, plus a mutation engine that walks a seed file you supply. It works as a test-case factory: cases are written as numbered files into a directory you serve over HTTP, and the browser advances through them using the refresh method — when the browser crashes, the web server's access log identifies the offending case number. Attended mode (`-A`) covers cases needing interaction such as JS alert()/prompt(); unattended (`-U`) is fire-and-forget. This is 2010-era software: expect dated grammars and treat it as a generator and template rather than a modern bug finder.

## Typical use cases

### Enumerate modules and oracle contents

See what the fuzzer can target.

```
bf3 -D
```

Outcome: lists the five target modules with their IDs (1 CSS, 2 DOM, 3 HTML, 4 JavaScript, 5 XML); `bf3 -T` likewise lists the fuzzing oracle contents.

### Fuzz JavaScript unattended with the oracle

Mass-produce oracle-driven JavaScript cases.

```
bf3 -M 4 -U -O -P /srv/http/js
```

Outcome: writes numbered JavaScript test cases into /srv/http/js; point a browser at the directory so it refreshes case by case, and after a crash check the HTTP server access log to recover the active case number.

### Random-generation DOM fuzzing in attended mode

Cover interaction-dependent cases with random data.

```
bf3 -M 2 -A -R 10000 -Z 10000 -P /srv/http/dom
```

Outcome: generates up to 10,000 DOM test cases with random data of up to 10,000 bytes each into /srv/http/dom for attended (click/alert-friendly) fuzzing.

### Mutate a seed file

Fuzz around a known-good input instead of generating from scratch.

```
bf3 -M 1 -U -R 100 -X samples/css/style.css -x samples/css/bmgsec.html -P /srv/http/cssm
```

Outcome: mutates style.css character by character (total cases = `-R` value × characters in the file), wrapping each mutation in the `-x` HTML file for display; cases are numbered under /srv/http/cssm.

*No corpus evidence — derived from upstream documentation.*
