---
name: "jbrute"
categories:
  - blackarch-cracker
version: "0.99"
homepage: "http://sourceforge.net/projects/jbrute/"
packaged-by: [blackarch]
blackarch-builddate: "2025-11-30"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "AnkitaB/JBrute"
github-stars: 0
github-pushed: "2014-07-20"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jbrute

> Standalone Java hash cracker and hash-type identifier.

## What it's for

Audits hashed passwords with no dependencies beyond a Java runtime — it ships
as a single JBrute.jar behind a `jbrute` wrapper (by Gonzalo Camino). It
identifies which algorithm a hash belongs to, encrypts words for verification,
and cracks hashes with dictionary attacks (optionally rule-based, using
John-the-Ripper-style rules) or brute force over selectable charsets and
length ranges, multi-threaded. Algorithm codes cover MD5, MD4, SHA-1, SHA-256,
SHA-512, MD5-crypt, NTLM, LM, Oracle 10g/11g, MSSQL 2000/2005/2012, MySQL
322/411, PostgreSQL, Sybase and Informix, with pre/post-salting and chained
hashing. Caveat: the encrypt/decrypt code paths rely on the Java 8-era JAXB
runtime — on current Java releases the analysis options (`--guess`,
`--list_charsets`, `--expected`) still work but cracking threads abort with
`NoClassDefFoundError`; use a Java 8 runtime for actual cracking.

## Typical use cases

### Identify the algorithm of a hash

Determine what kind of hash you are looking at.

```
jbrute --guess --hash=5f4dcc3b5aa765d61d8327deb882cf99
```

Outcome: prints every algorithm the digest could be (for this MD5 input:
`MD5,MD4,NTLM,LM`); add `--lucky` to the same invocation to narrow the output
to the single most probable algorithm (`Lucky mode: ON`).

### Crack a hash with a dictionary and rules

Run a wordlist attack with mangling rules.

```
jbrute --decrypt --algorithm=1 --hash=5f4dcc3b5aa765d61d8327deb882cf99 --method=dictionary --dict_file=wordlist.txt --threads=4
```

Outcome: loads the dictionary (default `wordlist.txt`) and rule file (default
`rules.txt`), reports how many words and rules were read, then tries every
word plus its rule mutations and prints any recovered plaintext with the
elapsed time.

### Brute-force with charset and length bounds

Search a keyspace directly when no wordlist fits.

```
jbrute --decrypt --algorithm=C --hash=8846f7eaee8fb117ad06bdd830b7586c --method=brute --charset=loweralpha-numeric --minlength=1 --maxlength=8 --threads=8
```

Outcome: prints the detected core count, thread count and charset, then
enumerates every candidate in the given length range until the hash is cracked
or the keyspace is exhausted; `--list_charsets` prints the available charsets
(numeric, loweralpha, mixalpha-numeric, ascii-32-95, ...).

### Benchmark the cracking speed

Estimate throughput before committing to a long run.

```
jbrute --test --algorithm=1 --time=5 --threads=4
```

Outcome: hashes random candidates for the given number of seconds and reports
the estimated hash rate for that algorithm and thread count, which helps size
realistic `--maxlength` bounds for brute-force runs.
