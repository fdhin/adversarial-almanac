---
name: "pack2"
categories:
  - passwords
version: "0.1.0~git20200929.da4b245-0kali4"
homepage: "https://github.com/hops/pack2"
origin: "kali"
recipe: "kali-debian"
packaged-by: [kali]
build-system: rust
meta-checked: "2026-11-09"
github-repo: "hops/pack2"
github-stars: 38
github-pushed: "2020-09-29"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pack2

> Rust rewrite of PACK: wordlist statistics, mask filtering and $HEX[] decoding as Unix-style filters.

*No corpus evidence — derived from upstream documentation.*

## What it's for

pack2 is a work-in-progress Rust replacement for iphelix's PACK, started for the Crack Me If You Can 2020 contest. It currently implements the statistics half of PACK, not rule generation — the author points to synacktiv/rulesfinder (also Rust) for that. The design is deliberately filter-like: every subcommand reads stdin when no input file is given, writes stdout when no output file is given, sends human-readable statistics to stderr, and transparently decodes hashcat-style `$HEX[]`-encoded input lines before processing. Subcommands: `statsgen`, `filtermask` and `unhex` (`pack2 help <subcommand>` lists each one's options). Being unfinished upstream software, expect rough edges — notably `filtermask`, which the README itself flags as broken in many cases.

## Typical use cases

### Get wordlist statistics

Summarize a leak the way PACK's statsgen does.

```
pack2 statsgen < leaked.txt
```

Outcome: prints length, charset and simple-mask distributions plus the top masks to stderr, and writes the tab-separated mask/occurrence table to stdout — redirect stdout (or use `-o`) to save the mask list for the next tool in the pipeline.

### Save the mask table to a file

Keep the machine-readable output apart from the human summary.

```
pack2 statsgen -o leaked.masks leaked.txt
```

Outcome: writes the mask/occurrence table to leaked.masks while the human-readable summary still streams to stderr, so pipelines stay clean.

### Filter a wordlist by hashcat mask

Keep only the candidates matching a given structure.

```
pack2 filtermask ?l?l?l?l?l?d leaked.txt
```

Outcome: prints only lines whose characters match the mask (here five lowercase letters followed by a digit), e.g. to pre-shape a wordlist to a target's observed password pattern; mind the upstream warning that this subcommand misbehaves in many cases (issue #6) — sanity-check the output count afterwards.

### Decode $HEX[] lines

Normalize hashcat hex-encoded candidates.

```
echo '$HEX[52c3b67363687469]' | pack2 unhex
```

Outcome: writes the decoded line (the README example yields `Röschti`); lines not in `$HEX[]` form pass through unchanged — useful before feeding a wordlist to tools that do not understand the encoding. Conversely, any output line containing characters outside printable ASCII is re-encoded into `$HEX[]` form by the other subcommands.
