---
name: "choronzon"
categories:
  - blackarch-fuzzer
version: "4.d702c31"
homepage: "https://github.com/CENSUS/choronzon"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "CENSUS/choronzon"
github-stars: 274
github-pushed: "2024-05-05"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# choronzon

> An evolutionary, knowledge-based file-format fuzzer by CENSUS Labs.

## What it's for

Choronzon fuzzes applications that parse structured file formats. It models a
file format as a tree of "genes" (a PNG parser ships as an example), alters
seed files with classic mutators (byte flips, swaps, random data) and
structure-aware recombinators (chunk duplication, removal, swapping), then
keeps the generations that score best under fitness algorithms driven by
coverage feedback — Intel Pin basic-block tracing correlated back to the
binary via IDA Pro. It is Python 2 research code with heavyweight external
dependencies (Pin, IDA Pro), so it is a campaign tool for patient file-format
work, not a turnkey fuzzer. Everything runs from a Python configuration file
that names the campaign, the parser module, the seed corpus and the
mutator/recombinator sets; new file formats are supported by writing a
Gene/Serializer/Deserializer module in `chromosome/parsers/`.

## Typical use cases

### Show the command-line interface

The tool takes exactly one argument: a configuration file.

```
choronzon --help
```

Outcome: argparse prints the usage for the single positional argument
`config` (`/path/to/config/file.py`); nothing else runs.

### Run a fuzzing campaign with the bundled PNG example

Starts an evolutionary campaign over a corpus of PNG seed files against a
target configured in the example settings.

```
choronzon /usr/share/choronzon/settings/pngcheck.py
```

Outcome: prints `[+] Choronzon fuzzer v.0.1` and `[+] starting campaign...`,
copies the configured `InitialPopulation` seed directory into a new campaign
directory, then loops mutate → execute → evaluate generations indefinitely;
Ctrl+C stops it with `[+] Bye! :)`. When `KeepGenerations` is enabled, each
generation's chromosomes are written to a per-epoch directory under the
campaign directory.

*No corpus evidence — derived from upstream documentation.*
