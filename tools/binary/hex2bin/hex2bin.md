---
name: "hex2bin"
categories:
  - blackarch-binary
version: "2.5"
homepage: "http://hex2bin.sourceforge.net/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2017-11-23"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "algodesigner/hex2bin"
github-stars: 50
github-pushed: "2025-01-10"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hex2bin

> Convert Intel HEX and Motorola S-record firmware files into flat binary images, with padding and checksum control.

## What it's for

hex2bin (with its sibling mot2bin) converts microcontroller firmware in Intel
HEX format — segmented or linear extended addressing — or Motorola S-record
format into a flat binary file suitable for EPROM/flash programmers. Records
need not be sorted and gaps between records are allowed: the tool buffers the
bytes, pads holes with a configurable fill value (default FF, the erased state
of EPROM/EEPROM/flash), and can force the binary's start address or total
length. It also verifies record checksums and can compute and insert a
checksum or CRC into the output image. Options are case sensitive and take a
space before the parameter; all values are hexadecimal without a `0x` prefix
(`0100`, not `0x0100`). For more input formats, see the larger SRecord suite.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Convert an Intel HEX file to binary

```
hex2bin firmware.hex
```

Outcome: writes `firmware.bin`, spanning from the lowest address present in
the hex file; intermediate extensions are dropped (`file.test.hex` becomes
`file.bin`).

### Force the binary's start address

```
hex2bin -s 0000 start_at_0100.hex
```

Outcome: pads the output from address 0000 to the lowest record address with
fill bytes so the first byte lands where the target device expects it — this
is the output-image start address, distinct from the hex file's execution
start-address record.

### Pad the image to a fixed length

```
hex2bin -l 0800 ends_before_07FF.hex
```

Outcome: extends the binary with FF (or the value set by `-p`) up to the
device's memory size, matching an erased flash image.

### Verify record checksums during conversion

```
hex2bin -c example.hex
```

Outcome: reports checksum errors found in the source records while still
completing the conversion — the default is to ignore them for quick manual
edits.

### Insert a computed CRC or checksum into the image

```
hex2bin -k 3 -f 1FF0 -C 1021 FFFF f f f 0000 firmware.hex
```

Outcome: computes a CRC16 (poly 1021, init FFFF, no reflection, xorout 0000)
over the image and stores the check value at address 1FF0, little/big endian
chosen with `-E`. `-k` selects 8/16-bit checksum or CRC8/16/32; `-F` writes a
forced literal value instead, and `-r` bounds the computed range.

### Convert a Motorola S-record file

```
mot2bin firmware.s19
```

Outcome: converts S19 files (16/24/32-bit address records) to binary using the
same options as hex2bin, some of which are specific to Motorola files. In
scripts, add `-b` so a missing input aborts with exit code 1 instead of
prompting for another filename, and `-w` to byte-swap pairs for
word-swapped targets.
