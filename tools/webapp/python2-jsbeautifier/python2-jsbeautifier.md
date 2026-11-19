---
name: "python2-jsbeautifier"
categories:
  - blackarch-webapp
version: "1.13.4"
homepage: "https://github.com/beautify-web/js-beautify"
packaged-by: [blackarch, arch, nixpkgs]
blackarch-builddate: "2021-01-18"
arch-builddate: "2026-07-01"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "beautify-web/js-beautify"
github-stars: 9007
github-pushed: "2026-10-19"
github-archived: false
github-issues: 452
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 102
gh-activity-checked: "2026-11-09"
---

# python2-jsbeautifier

> Python 2 build of js-beautify: the classic JavaScript beautifier/unobfuscator as a CLI and library.

## What it's for

Recovering readable JavaScript from minified or lightly obfuscated code — webshells, tracker blobs, deobfuscated page scripts. This package pins the last Python-2-compatible release (1.13.4) of the beautify-web/js-beautify project, giving legacy hosts a `js-beautify` CLI and the `jsbeautifier` library. It reindentation-forms JavaScript only (the Python port does not handle HTML or CSS, unlike the JS sibling), supports `.jsbeautifyrc`-style editorconfig integration, and can unpack common packer wrappers so `eval(...)` blobs become plain source. On BlackArch the binary is installed as `js-beautify2` to coexist with the Python 3 package; on PyPI (`pip install jsbeautifier`) the entry point is `js-beautify`.

## Typical use cases

### Beautify a file to stdout

Quick one-off readability pass on a suspicious script.

```
js-beautify2 obfuscated.js
```

Outcome: prints the reformatted JavaScript with consistent indentation and preserved/normalized newlines to standard output; reading it as a pipe (`cat obfuscated.js | js-beautify2`) works when no filename is given.

### Rewrite files in place

Clean up a whole directory of minified scripts.

```
js-beautify2 -r '*.min.js'
```

Outcome: with `-r/--replace` each matched file is overwritten with its beautified version (globs supported); redirection to an output file is available via `-o/--outfile` when you want to keep the original.

### Control formatting

Match the project's style instead of the defaults.

```
js-beautify2 -s 4 -t obfuscated.js
```

Outcome: reformats with 4-space indent (`-s/--indent-size`) and tab indentation (`-t/--indent-with-tabs`); related knobs include `-d/--disable-preserve-newlines` and `-k/--keep-array-indentation` — combine with `-o pretty.js` to write the result elsewhere.

### Deobfuscate from Python code

Use it as a library inside a Python 2 analysis script.

```
python2 -c 'import jsbeautifier; print(jsbeautifier.beautify(open("obfuscated.js").read()))'
```

Outcome: the `jsbeautifier.beautify()` call returns the beautified source as a string, so unpacking/reformatting can be embedded in larger triage pipelines instead of shelling out.

*Provenance: no corpus evidence — derived from upstream documentation (README Python section + 1.13.4 `jsbeautifier/__init__.py` getopt CLI, verified 2026-11).*
