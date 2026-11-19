---
name: "morxkeyfmt"
categories:
  - blackarch-crypto
version: "1.0"
homepage: "http://www.morxploit.com/tools/"
packaged-by: [blackarch]
blackarch-builddate: "2015-02-06"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# morxkeyfmt

> Reads Bitcoin private keys from stdin and re-emits them in any format — hex, WIF, public key or address.

## What it's for

A small filter script (originally `keyfmt` from the morxploit toolset) for
bulk-formatting Bitcoin private keys. It reads one key per line from stdin —
either a 64-character hex private key or a 51-character WIF key — and applies a
printf-style format string with the variables `%h` (hex privkey), `%w` (WIF
privkey), `%p` (public key) and `%a` (Bitcoin address). Its purpose is
interoperability: turning raw hex keys into wallet-importable forms or
vanitygen-style listings without a full wallet. Key derivation uses secp256k1
and requires the `ecdsa` Python module only when `%p` or `%a` appear in the
format string. The script is Python 2 (the packaged dependency is python2), and
the original morxploit.com site no longer hosts it — the packaged script is the
reference. It formats and converts; it does not sign transactions or manage a
wallet.

## Typical use cases

### Convert a hex private key to address and WIF

Turn a raw hex key into a human-readable address-plus-WIF pair, the layout
vanitygen prints.

```
echo 0000000000000000000000000000000000000000000000000000000000000001 | morxkeyfmt 'Address: %a\nPrivkey: %w'
```

Outcome: prints one `Address: <address>` and `Privkey: <WIF>` pair per input
key; the `\n` in the format string becomes a real newline in the output.

### Produce Electrum-importable key lines

Format a list of keys so each line reads `a:<WIF>`, the form Electrum's
sweep/import accepts.

```
morxkeyfmt 'a:%w' < keys.txt
```

Outcome: one `a:<WIF>` line per key from `keys.txt`, ready to paste into
Electrum's import field.

### Generate and format a fresh random key

Create a brand-new random key and print it as `address:WIF` in one pipeline
(the idiom documented in the script header).

```
hexdump -v -e '/1 "%02X"' -n 32 /dev/urandom | morxkeyfmt '%a:%w'
```

Outcome: a single line containing a newly generated Bitcoin address and its
WIF private key.

*No corpus evidence — derived from upstream documentation.*
