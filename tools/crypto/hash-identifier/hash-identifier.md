---
name: "hash-identifier"
categories:
  - blackarch-crypto
version: "6.0e08a97"
homepage: "https://github.com/blackploit/hash-identifier"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "blackploit/hash-identifier"
github-stars: 638
github-pushed: "2024-05-08"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hash-identifier

> Classic Python script (Zion3R/blackploit) that guesses a hash's type from its length, character set and prefix.

## What it's for

A small script for the first step of hash work: you have a digest and need to know what to feed hashcat, john or a lookup site. It heuristically matches the digest's length, charset and prefix against a large catalog of formats — plain algorithms (MD5, SHA-1, SHA-256, Whirlpool, GOST R 34.11-94, NTLM, MySQL5...) and application-specific ones (MD5(phpBB3), MD5(Wordpress), MD5(APR), Joomla, Django SHA-*, Domain Cached Credentials, SAM `LM:NT` pairs). It ranks candidates under "Possible Hashs" and "Least Likely", and warns that some formats cannot be told apart until decrypted. It identifies only — it never cracks anything.

## Typical use cases

### Identify a hash passed as an argument

Classify one digest non-interactively, e.g. a 64-hex value pulled from a dumped credential.

```
hash-identifier e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
```

Outcome: prints the banner, then `Possible Hashs:` headed by `[+] SHA-256` with other 64-hex candidates (Haval-256, SNEFRU-256, RipeMD-256...), followed by a `Least Likely` section — take the top hit as the format to use in hashcat or john.

### Work interactively through a batch of hashes

Start the tool bare and paste digests at the prompt, one per line.

```
hash-identifier
```

Outcome: the tool shows a `HASH:` prompt and re-prompts after every digest pasted, classifying each in turn — convenient when triaging a credential dump by hand, since you can keep pasting without restarting.

### Distinguish password-file formats before cracking

Use it on digests still in their native crypt encoding, such as `$1$...` MD5-crypt strings from a passwd file.

```
hash-identifier '$1$12345678$X5kSgFf4CzOwE6YABmQxP1'
```

Outcome: the crypt prefix and structure drive the match, returning `[+] MD5(Unix)` — the application-specific format name rather than plain MD5 — which determines the correct hashcat mode or john format string for the next step.
