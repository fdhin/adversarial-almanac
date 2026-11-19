---
name: "jsfuck"
categories:
  - blackarch-misc
version: "242.6f8fdf3"
homepage: "https://github.com/aemkei/jsfuck"
packaged-by: [blackarch]
blackarch-builddate: "2025-03-15"
build-system: scripts
license: custom:WTFPL
meta-checked: "2026-11-09"
github-repo: "aemkei/jsfuck"
github-stars: 8598
github-pushed: "2025-03-10"
github-archived: false
github-issues: 38
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jsfuck

> Encode any JavaScript into just six characters — `[ ] ( ) ! +` — and still have it execute.

## What it's for

JSFuck (aemkei) is an esoteric, educational encoder that exploits JavaScript type coercion to rewrite arbitrary code using only the characters `[]()!+`. The output runs anywhere JavaScript runs — browsers and Node.js alike — because it relies only on language semantics, not on a particular runtime. Practical uses are filter and WAF testing (does your detector still recognize an encoded payload?) and learning the coercion tricks it is built on; the encoding is deliberately enormous and slow, so treat it as a probe and a teaching tool rather than a stealth technique.

## Typical use cases

### Serve the interactive web demo locally

```
jsfuck 8080
```

Outcome: starts a local web server on 127.0.0.1:8080 serving the official demo page — type JavaScript into one box and get its six-character encoding in the other, with live length readouts.

### Encode from Node

```
node -e "console.log(require('/usr/share/jsfuck/jsfuck.js').JSFuck.encode('alert(1)', true, true))"
```

Outcome: prints the `[]()!+` encoding of the input string; the optional second and third arguments wrap the result in `eval` and run it in the parent scope, so pasting the output into a JavaScript console executes `alert(1)`.

*No corpus evidence — derived from upstream documentation.*
