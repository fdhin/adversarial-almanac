---
name: "ctf-party"
categories:
  - blackarch-misc
version: "5.0.0.r55.gd55b7b4"
homepage: "https://noraj.github.io/ctf-party/"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: ruby
license: MIT
meta-checked: "2026-11-09"
github-repo: "noraj/ctf-party"
github-stars: 89
github-pushed: "2026-11-08"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 64
gh-activity-checked: "2026-11-09"
---

# ctf-party

> Ruby CLI and library that turns everyday encoding and crypto transformations into one-liner method calls.

*No corpus evidence — derived from upstream documentation.*

## What it's for

ctf-party (by Alexandre ZANNI, noraj) patches Ruby's String class with short
aliases for the transformations constantly needed while writing exploits and
quick scripts — base64, hex, binary, URL encoding, rot13, hashes and friends — so
`myvar.to_b64!` replaces a multi-line round-trip through require'd stdlib
modules. Everything is exposed two ways: as String methods in Ruby code, and as a
`ctf-party` shell command that chains transformations left to right over its
input. Pure native Ruby with no gem dependencies; it is a transformation helper,
not a cracker or scanner.

## Typical use cases

### Chain transformations from the shell

Apply several conversions without writing a script.

```
ctf-party 'NzQ2Zjc0NmY=' from_b64 hex2str str2bin
```

Outcome: applies the named methods left to right — base64-decode, hex to string,
string to binary — and prints `01110100011011110111010001101111`. The input
string is always the first argument; any method name the library defines works as
a subcommand (`to_hex`, `from_b64`, ...).

### Encode inside a Ruby script

Skip the stdlib boilerplate in an exploit.

```
ruby -r ctf_party -e 'puts "string".to_b64!'
```

Outcome: prints `c3RyaW5n` — the String method handled the encoding in place.
All CLI subcommands exist as String methods, so shell and library usage stay
interchangeable.
