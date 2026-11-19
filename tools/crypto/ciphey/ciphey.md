---
name: "ciphey"
categories:
  - crypto
homepage: "https://github.com/bee-san/Ciphey"
origin: "awesome-pentest"
recipe: "upstream"
packaged-by: [aur, nixpkgs]
build-system: rust
github-repo: "bee-san/Ciphey"
github-stars: 21759
github-match: "exact"
gh-checked: "2026-11-14"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-14"
license: MIT
---

# ciphey

> Paste in text that has been encoded or encrypted — Ciphey works out how, peels every layer, and hands you the plaintext with the decoder path it took.

## What it's for

Ciphey (the Rust rewrite of the well-known Python tool, now under the author's bee-san account) automatically breaks encoded and weakly-encrypted strings without being told the cipher: Base64 (standard/URL-safe), Base32/58/91/65536, Z85, hex, binary, URL-encoding, Morse, Braille, A1Z26, Citrix CTX1, Caesar/ROT13/ROT47, Atbash, Vigenère (key worked out automatically), rail fence, reversed text and Brainfuck — several layers deep. It runs an A* search over chains of decoders in parallel, stops at the first candidate that passes its plaintext checks, and each accepted plaintext is additionally classified by LemmeKnow (120+ formats: TOTP URIs, tokens, IPs, credentials in command lines, flag formats). The first run creates `~/.ciphey/config.toml` (theme, prompt behaviour); results are cached in `~/.ciphey/database.sqlite`. It is for encoded/weak-crypto material — not for keyed modern encryption, and it gives up after a time limit (default 5 s, `-c` to change).

## Typical use cases

### Decode a pasted string

```
ciphey -t 'aGVsbG8gdGhlcmUgZ2VuZXJhbA=='
```

Outcome: Ciphey proposes the candidate plaintext and asks for confirmation (`y/N`); on accept it prints the plaintext plus the decoder used — for layered input, the full path (e.g. Base64 → Hexadecimal → Caesar) and any key such as the shift number.

### Decode a file non-interactively

```
ciphey -f secret.txt -d
```

Outcome: input is read from the file and `-d` disables the y/N prompt, taking the first plaintext found — script-safe, with no stdin interaction; `-c 15` raises the search time limit from the 5-second default.

### Constrain the search with a crib

Only accept plaintext matching a known format, e.g. a CTF flag prefix.

```
ciphey -r 'flag\{' -t '...'
```

Outcome: the generic plaintext checks switch off and the search ends only when a decoded candidate matches the regex — finds plaintext the English checks would reject and disambiguates when several candidates look plausible.

### Accept wordlist hits as plaintext

```
ciphey --wordlist words.txt -t '...'
```

Outcome: any candidate that exactly matches a line of the wordlist is accepted as the answer — useful when the expected plaintext is a name, identifier or token rather than English prose.

*No corpus evidence — derived from upstream documentation.*
