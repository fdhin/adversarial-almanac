---
name: "mfcuk"
categories:
  - blackarch-wireless
version: "0.3.8"
homepage: "http://code.google.com/p/mfcuk/"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2020-12-26"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "nfc-tools/mfcuk"
github-stars: 1129
github-pushed: "2024-07-10"
github-archived: false
github-issues: 40
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mfcuk

> MIFARE Classic Universal toolKit — recovers unknown MIFARE Classic keys with the Crypto-1 "darkside" attack through a libnfc reader.

*No corpus evidence — derived from upstream documentation.*

## What it's for

mfcuk (by Andrei Costin, with Nethemba's AC/AM code and packaging) implements the darkside attack against MIFARE Classic cards: exploiting the Crypto-1 cipher's predictable error behavior, it recovers an unknown sector key from a tag you can present to a reader repeatedly, then chains recovered keys into further sectors. It works with any libnfc-supported reader and complements mfoc — the nested-authentication attack that instead needs one known key as a starting point. It can also compute keys offline from a captured Proxmark3 authentication exchange. It is not a passive sniffer and cannot recover keys from a card you cannot query.

## Typical use cases

### Recover the key of one sector

Crack key A of sector 0 on a tag placed on the reader.

```
sudo mfcuk -C -R 0:A -v 2
```

Outcome: connects to the reader (`-C` is required for recovery), runs the darkside attack against sector 0 key type A, and prints the recovered 12-hex-digit key; `-v 2` raises verbosity so progress is visible.

### Recover every key on the card

Iterate over all sectors and both key types.

```
sudo mfcuk -C -R -1
```

Outcome: recovers keys for every sector (`-1` means all sectors, key types A and B), reporting each key on stdout as it falls.

### Slow the field timing for stubborn readers

Retry with longer field on/off pauses when recovery stalls.

```
sudo mfcuk -C -R 0 -s 250 -S 250
```

Outcome: sleeps 250 ms at each field-off (`-s`) and field-on (`-S`) transition — the upstream usage notes the longer delays can produce results where default timing fails.

### Verify keys, trying defaults first

Check which sectors still open with factory keys.

```
sudo mfcuk -C -V -1 -D
```

Outcome: verifies keys for all sectors, trying the built-in default-key list first (`-D`) before reporting failures; sectors that verify are listed as recovered.

### Recover a key offline from a Proxmark3 capture

Derive the key from a sniffed authentication exchange without any reader attached.

```
mfcuk -P 0x5c72325e:0x50829cd6:0xb8671f76:0xe00eefc9:0x4888964f
```

Outcome: parses the uid:tag_chal:nr_enc:reader_resp:tag_resp hex literals and runs the offline Crypto-1 computation (the upstream example recovers key FFFFFFFFFFFF); `-p <proxmark3.log>` does the same directly from a Proxmark3 log file.
