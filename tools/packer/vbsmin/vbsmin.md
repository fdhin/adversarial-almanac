---
name: "vbsmin"
categories:
  - blackarch-packer
version: "v1.2.0.r33.gc609b55"
homepage: "https://noraj.github.io/vbsmin/"
packaged-by: [blackarch]
blackarch-builddate: "2026-11-01"
build-system: ruby
license: MIT
meta-checked: "2026-11-09"
github-repo: "noraj/vbsmin"
github-stars: 25
github-pushed: "2026-11-02"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 35
gh-activity-checked: "2026-11-09"
---

# vbsmin

> Minifier for VBScript source, usable as a Ruby CLI or library.

## What it's for

vbsmin (by Alexandre Zanni, noraj) shrinks VBScript files: it strips trailing and leading whitespace, blank lines, and comments (`'` at line start, inline `'`, and `REM`), and collapses multi-line constructs — line continuations (underscore) and colon-joined statements — into single lines. The result is written next to the input as `<name>.min.vbs`. Typical motivation is not obfuscation but payload logistics: some injection channels (for example SQL writes that reject newlines) and file-size-limited delivery paths need the whole script on one short line. The CLI takes exactly one input file; there is no directory or glob mode. Ships as a Ruby gem with both the `vbsmin` command and a `VBSMin` library class.

## Typical use cases

### Minify a VBScript file

```
vbsmin payload.vbs
```

Outcome: prints the original and minified sizes plus bytes saved, and writes the result to `payload.min.vbs` in the same directory. Add `--no-color` for plain output or `--version` to check the gem version.

### Use it as a Ruby library

```ruby
require 'vbsmin'

vm = VBSMin.new
vm.minify('payload.vbs')
puts vm.min_filepath
```

Outcome: `minify` reads and rewrites the minified sibling file and returns its path; the `vm` object also exposes `input_size`, `output_size`, `diff_size`, `original_filepath` and `min_filepath` for reporting.

*No corpus evidence — derived from upstream documentation.*
