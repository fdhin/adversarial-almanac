---
name: "gf"
categories:
  - blackarch-misc
version: "39.dcd4c36"
homepage: "https://github.com/tomnomnom/gf"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2022-04-23"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "tomnomnom/gf"
github-stars: 2134
github-pushed: "2024-06-08"
github-archived: false
github-issues: 54
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gf

> A wrapper around grep that gives your common patterns short, memorable names.

## What it's for

gf (tomnomnom) stores the grep patterns and flags you use repeatedly as named JSON files under `~/.gf`, so a long regex plus its required flags collapse into a single word. It shines when auditing code bases or triaging large tool output: the pattern definitions are version-controllable and shareable, and grep's "no matches — typo or nothing to find?" ambiguity disappears because the pattern is fixed. gf only wraps grep (or an alternative engine you configure); it adds nothing grep cannot do — the value is the named, curated patterns. It ships with example patterns such as `urls`, `base64`, `php-sources` and `takeovers`.

## Typical use cases

### Run a named pattern over files

```
gf php-sources .
```

Outcome: greps recursively under the current directory using the stored flags and regex of the `php-sources` pattern (equivalent to `grep -HnrE '(\$_(POST|GET|COOKIE|REQUEST|SERVER|FILES)|php://(input|stdin))' .`), printing file names, line numbers and matching lines.

### Filter a stream

```
cat headers.txt | gf urls
```

Outcome: only lines matching the `urls` pattern pass through, each printed with its source intact — handy for triaging saved responses or the output of other tools.

### Save a new pattern from the command line

```
gf -save php-serialized -HnrE '(a:[0-9]+:{|O:[0-9]+:"|s:[0-9]+:")'
```

Outcome: writes `~/.gf/php-serialized.json` containing the flags and pattern, so `gf php-serialized` works immediately without editing any file by hand.

### Use a faster search engine per pattern

Add an `engine` key to a pattern file such as `~/.gf/aws-keys.json`:

```
{
  "engine": "ag",
  "flags": "-Hanr",
  "pattern": "([^A-Z0-9]|^)(AKIA|A3T|AGPA|AIDA|AROA|AIPA|ANPA|ANVA|ASIA)[A-Z0-9]{12,}"
}
```

Outcome: `gf aws-keys` dispatches to the silver searcher instead of grep with that engine's flag syntax — flags must be adjusted per engine (the `E` flag is dropped for `ag`).

*No corpus evidence — derived from upstream documentation.*
