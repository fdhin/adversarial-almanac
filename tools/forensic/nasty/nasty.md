---
name: "nasty"
categories:
  - forensics
version: "0.6+git20220929.9830b7a-3+b1"
homepage: "https://github.com/folkertvanheusden/nasty"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali, nixpkgs]
build-system: cmake
meta-checked: "2026-11-09"
github-repo: "folkertvanheusden/nasty"
github-stars: 5
github-pushed: "2022-09-29"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nasty

> Recover the forgotten passphrase of a secret key in your own GPG keyring by brute force.

## What it's for

nasty (Folkert van Heusden) recovers the passphrase of a secret key in the local GPG keyring when you still have the key but no longer remember what protects it — the classic "old backups encrypted with a key whose passphrase is gone" situation. It generates candidate passphrases and hands each one to gpgme, which tests it by attempting an actual detached-signature operation with the secret key (after telling gpg-agent to drop any cached passphrase), so a hit is a passphrase that genuinely works. Guessing can walk a character set incrementally, try candidates at random, or read candidates from a wordlist file. Progress is checkpointed to `nasty.state` in the working directory, so an interrupted run resumes where it stopped — and while that file exists, command-line switches are rejected until you delete it. It works through gpgme against a keyring you control; it is a self-recovery tool, not an offline cracker for arbitrary key material.

## Typical use cases

### Brute-force with a restricted character set

Sweep a keyspace you can describe.

```
nasty -c aA0 -a 8 -b 12
```

Outcome: selects the first secret key in the keyring (printing its key ID and user ID) and tries every 8–12 character passphrase over lowercase, uppercase and digits — `-c` restricts the default charset (printable ASCII 32–255) — reporting `# tried: N ..., last tried: <phrase>` progress; on success it prints `Passphrase is: <phrase>`.

### Attack a specific key

Pick the key instead of taking the first one.

```
nasty -k user@example.com
```

Outcome: filters the secret-key listing by the given pattern (substring of key ID, name or email, as in gpg key listings) and starts guessing against the matching key with default settings.

### Try passphrases from a wordlist

The cheap first pass before any brute force.

```
nasty -m file -i phrases.txt -f found.txt
```

Outcome: reads candidate passphrases one per line from `phrases.txt`, tests each against the selected key, prints the working passphrase when found and additionally writes it to `found.txt` as `passphrase=...`; `-m random` switches to random-order guessing over the charset instead.

*No corpus evidence — derived from upstream documentation.*
