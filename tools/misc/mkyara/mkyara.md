---
name: "mkyara"
categories:
  - blackarch-misc
version: "3.8147f91"
homepage: "https://github.com/fox-it/mkYARA"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: python
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "fox-it/mkYARA"
github-stars: 221
github-pushed: "2021-10-07"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mkyara

> Generates YARA rules from executable code, automatically wildcarding every byte that is not static between samples.

## What it's for

Turning malware code into YARA signatures without hand-wildcarding. Raw executable bytes cannot be pasted into a YARA rule because memory addresses, offsets and other variable values change between samples; mkYARA disassembles a code chunk (via Capstone) and wildcards all the pieces that are not static, producing a ready-to-use rule. It ships in three forms: a standalone `mkyara` command that generates a rule from a byte range of a file, a Python library for building rules from arbitrary chunks in memory, and an IDA plugin for generating rules from selected instructions. Wildcard aggressiveness is adjustable: `loose` wildcards all operands, `normal` only displacement operands, `strict` only jump/call target addresses.

## Typical use cases

### Generate a rule from a code chunk in a binary

Turn a known-malicious code region into a YARA signature.

```
mkyara -i x86 -a 32 -f sample.bin -o 4096 -s 150 -m normal -n my_signature -r rule.yar
```

Outcome: disassembles 150 bytes at file offset 4096 of sample.bin as 32-bit x86, wildcards displacement operands, and writes the generated rule (named my_signature) to rule.yar; omit `-r` to pick it up on stdout.

### Generate a strict rule

Keep the signature as literal as possible.

```
mkyara -f sample.bin -o 4096 -s 150 -m strict
```

Outcome: produces a rule that wildcards only jump and call target addresses, leaving every other byte literal — best for unique code, at the cost of fewer matches across recompiled variants.

### Build rules programmatically

Construct rules from in-memory chunks inside an analysis pipeline.

```python
from mkyara import YaraGenerator
from capstone import CS_ARCH_X86, CS_MODE_32

gen = YaraGenerator("normal", CS_ARCH_X86, CS_MODE_32)
gen.add_chunk(b"\x90\x90\x90", offset=1000)
gen.add_chunk(b"\x90\x90\x90\xff\xd7", is_data=True)
rule = gen.generate_rule()
print(rule.get_rule_string())
```

Outcome: assembles the chunks into one rule (marked `is_data` for non-code bytes) and returns its text via `get_rule_string()`, ready to feed a YARA scanner or TIP.

*No corpus evidence — derived from upstream documentation.*
