---
name: "python2-utidylib"
categories:
  - blackarch-misc
version: "0.6"
homepage: "https://github.com/nijel/utidylib"
packaged-by: [blackarch, arch]
blackarch-builddate: "2024-12-15"
arch-builddate: "2026-10-11"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "nijel/utidylib"
github-stars: 15
github-pushed: "2026-11-07"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-09"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 229
gh-activity-checked: "2026-11-09"
---

# python2-utidylib

> Python 2 bindings for the HTML Tidy parser and cleaner.

## What it's for

uTidylib wraps the HTML Tidy library (both classic Tidy and the HTML5-enabled tidy-html5) so Python code can diagnose and repair malformed HTML: missing end tags, unterminated elements, invalid nesting, sloppy casing. It turns tag-soup documents into clean, valid XHTML, which makes it useful as a preprocessing step before feeding scraped or hostile HTML to a strict parser. This package is the Python 2 build, kept in security distributions as a dependency for older tools; the project's original BerliOS hosting is long gone and development continues upstream on the Python 3 line. It is a library imported as `tidy` — there is no standalone command-line interface of its own.

## Typical use cases

### Repair malformed HTML in a Python 2 script

Normalize a broken fragment into a valid XHTML document.

```
python2 -c "
import tidy
print(tidy.parseString('<Html>Hello Tidy!', output_xhtml=1, add_xml_decl=1, indent=1, tidy_mark=0, doctype='transitional'))
"
```

Outcome: prints the input with an XML declaration and doctype added, casing and structure corrected — keyword arguments map directly to HTML Tidy's own configuration flags.

*No corpus evidence — derived from upstream documentation.*
