---
name: "assless-chaps"
categories:
  - cracker
homepage: "https://github.com/sensepost/assless-chaps"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: rust
license: see repository
meta-checked: "2026-11-13"
github-repo: "sensepost/assless-chaps"
github-match: "exact"
github-stars: 143
gh-checked: "2026-11-13"
worth: 5.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# assless-chaps

> Recovers the NT hash behind an MSCHAPv2/NTLMv1 challenge-response in milliseconds by splitting the DES keyspace and looking the rest up in a hash database.

## What it's for

Assless CHAPs (SensePost, Defcon 29 RF Hacking Village) cracks
MSCHAPv2/NTLMv1 challenge-response pairs — the classic catch from
hostile WiFi access points (EAP-WPE attacks) or responder captures —
without needing the plaintext password at all: any NT hash that produces
the observed response authenticates. It exploits how MSCHAPv2 splits the
NT hash into two 7-byte DES keys plus one 2-byte key; it brute-forces the
2-byte key (65,535 tries), then checks only database hashes ending in
those two bytes. With a prebuilt SQLite NT-hash database (rockyou, HIBP)
it answers in ~20-80ms where hashcat takes minutes, because it is a
lookup, not a crack. It does NOT crack plaintext passwords or NTLMv2;
you get the NT hash, which is itself enough to authenticate as the
victim on MSCHAPv2/NTLMv1 infrastructure.

*No corpus evidence — derived from upstream documentation.

## Typical use cases

### Crack a captured MSCHAPv2 exchange

Turn a challenge/response pair into the NT hash.

```
assless-chaps 5d79b2a85966d347 556fdda5f67d2b746ca3315fd8b93adcab5c792790a92e87 hashes.db
```

Outcome: brute-forces the third DES key (prints e.g. `Found in 22636
tries: 586c`), filters the database to hashes ending in those bytes, and
prints the matching full NT hash (`8846f7eaee8fb117ad06bdd830b7586c` —
the NT hash of `password`) — use it directly for pass-the-hash or to
authenticate to the service the capture came from.

### Speed up repeats with the two-byte lookup file

Skip most of the DES brute force (Python version).

```
python3 assless-chaps.py 5d79b2a85966d347 556fdda5f67d2b746ca3315fd8b93adcab5c792790a92e87 hashes.db twobytes
```

Outcome: uses the bundled frequency-sorted list of all 65,535 two-byte
values to order the search, typically saving rounds of DES — worthwhile
when processing many captured exchanges.

### Build a hash database from HIBP NT-hash lists

Prepare the lookup database.

```
sed "s/^\(.\{14\}\)\(.\{14\}\)\(.\{4\}\):.*/\3,\1,\2/" pwned-passwords-ntlm-ordered-by-hash.txt > hibp.csv
python3 mksqlitedb.py hibp.db hibp.csv
```

Outcome: reshapes HIBP's `hash:count` lines into the three-column CSV
(last-2-bytes, first-7, second-7) and builds the SQLite database
assless-chaps queries — budget disk: the DB runs ~61% larger than the
CSV (rockyou: 462MB CSV → 746MB DB).

### Derive the real challenge from an NTLMv1-SSP capture

Handle the SSP variant before cracking.

```
python3 ntlm-ssp.py 338d08f8e26de93300000000000000000000000000000000 cb8086049ec4736c
```

Outcome: combines the LM response and client challenge into the
effective server challenge (`The server challenge is: 724edf24aea0d68b`)
— recognize SSP by an LM response ending in zeros; feed the printed
challenge plus the NT response to assless-chaps as usual.

### Generate a hash database from any wordlist

Grow coverage with your own candidate passwords.

```
python3 nthash-from-clear.py wordlist.txt > hashlist.csv
python3 mksqlitedb.py custom.db hashlist.csv
```

Outcome: hashes every wordlist entry to its NT hash in the required CSV
layout and builds a database from it; for wordlist+rules coverage use
the README's hashcat mode-1000 patch flow (`hashcat -m1000
impossible_hash wordlist.txt -r best64.rule --potfile-disable --quiet >
out.csv`) to emit every candidate hash instead.
