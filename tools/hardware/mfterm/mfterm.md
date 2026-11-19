---
name: "mfterm"
categories:
  - hardware
version: "1.0.7"
homepage: "https://github.com/4ZM/mfterm"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "4ZM/mfterm"
github-stars: 154
github-pushed: "2024-01-14"
github-archived: false
github-issues: 20
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mfterm

> An interactive shell for MIFARE Classic 1k/4k tags — read, inspect, edit, clone and attack card contents through an NFC reader.

## What it's for

Working with MIFARE Classic tags from a terminal instead of per-step helper tools. It keeps state (a "current tag" in memory, "current keys" for authentication) and offers shell commands with tab completion and history: read a physical tag into memory, print or edit blocks, write the tag back, manage key sets, and run dictionary attacks against unknown sectors. It uses the libnfc-supported readers underneath, so it needs physical RF access to the card. Typical jobs: auditing what a card contains block by block, recovering sector keys with a key dictionary, and cloning a tag's contents to another card. It only handles the plain MIFARE Classic access model — it does not implement the Crypto-1 research attacks themselves (use mfoc/mfcuc for key recovery beyond dictionaries), and 1k tags are represented padded inside a 4k layout, matching other libnfc tools.

## Typical use cases

### Read a tag and inspect its contents

Start the shell, load keys, and dump the card:

```
mfterm
```

then inside the shell:

```
keys set a FFFFFFFFFFFF
read
print
```

Outcome: `read` authenticates sector by sector with the current keys and pulls the whole tag into memory; `print` renders the block/sector layout with data and trailing key blocks, and `print keys` shows the keys read from the tag's sector trailers. The tag in memory is what every other command operates on.

### Save, edit and write a tag dump

```
save card_dump.mfd
set 4 0d ea 12 00 12 00 12 04 00 12 34 56 78 9a bc de
write
```

Outcome: `save` persists the in-memory tag to a file (reload later with `load`), `set` overwrites block 4's bytes with the given data, and `write` pushes the in-memory tag back onto the physical card using the current keys — with `write unlocked` as the variant for block 0 on writable "magic" 1k Chinese cards.

### Crack unknown sector keys with a dictionary

```
dict load common_keys.txt
dict attack
```

Outcome: every key in the loaded dictionary (one 12-hex-char key per line, `#` comments allowed, multiple files union together) is tried against each sector; recovered keys land in the "current keys" so a following `read` succeeds. `keys test` verifies whether the current keys authenticate against the card in place.

*No corpus evidence — derived from upstream documentation.*
