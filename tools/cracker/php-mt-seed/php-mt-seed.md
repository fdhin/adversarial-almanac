---
name: "php-mt-seed"
categories:
  - blackarch-cracker
version: "4.0"
homepage: "http://www.openwall.com/php_mt_seed/"
packaged-by: [blackarch]
blackarch-builddate: "2019-01-23"
build-system: make
license: BSD
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# php-mt-seed

> Openwall's C cracker for PHP `mt_rand()` seeds: recovers the 32-bit PRNG seed from observed outputs so later "random" values — tokens, reset links, generated passwords — can be predicted.

*No corpus evidence — derived from upstream documentation (openwall.com/php_mt_seed; the source is also published as openwall/php_mt_seed on GitHub). Packaged binary name is `php-mt-seed`; upstream builds it as `php_mt_seed`.*

## What it's for

PHP's `mt_rand()` is a Mersenne Twister with a 32-bit seed. php_mt_seed inverts it: given one or more observed outputs — as exact values, ranges, and with the `$min,$max` arguments that were passed to `mt_rand()` — it enumerates every seed that could have produced them, searching the whole 32-bit space with SIMD (SSE2 through AVX-512) and OpenMP in well under a minute on a modern quad-core. It implements every algorithm revision (PHP 3.0.7–5.2.0, 5.2.1–7.0.x, 7.1.0+) and tags each recovered seed with the PHP versions it applies to. The classic abuse: an application exposes one mt_rand()-derived value (an anti-CSRF or session token) and later mints a secret (a password-reset token) without reseeding — crack the seed from the exposed value, then compute the secret from it. It does not crack password hashes; it cracks PRNG state.

## Typical use cases

### Recover the seed from a known first output

```
php-mt-seed 1328851649
```

Outcome: searches all 32-bit seeds across every PHP mt_rand() revision and prints each match tagged by version — for this upstream-documented value: `seed = 0x499602d2 = 1234567890 (PHP 5.2.1 to 7.0.x; HHVM)` plus the other version-specific matches, ending with a `Found N` count; the full space takes under a minute on a quad-core.

### Constrain by the mt_rand($min, $max) arguments

```
php-mt-seed 42 42 0 61
```

Outcome: interprets the four numbers as "first output exactly 42, drawn from mt_rand(0, 61)" — the shape used when a token picks characters from a 62-character alphabet — and keeps only seeds whose first constrained draw is 42; with just two numbers (`php-mt-seed 42 61`) the first output only has to fall in the range 42–61.

### Chain multiple outputs (and skip unknown ones)

```
php-mt-seed 1328851649 1328851649 0 2147483647 1423851145
```

Outcome: treats the first four numbers as an exact first output and the trailing 1423851145 as the exact second output; in the upstream example this narrows four candidate seeds down to just `1234567890`. Unknown outputs in between can be skipped with the always-true group `0 0 0 0`, which matches any output value.
