---
technology: GnuPG (OpenPGP)
kind: overview
audience: offensive-security
family: credential-stores
layer: "Keyring and file encryption (OpenPGP secret keys, S2K passphrase protection, armored ASCII keys, .gnupg keyring directory)"
language: C
first-released: 1999
homepage: "https://gnupg.org"
evidence: "9 writeups (strict command-context gate over merged text+ocr corpus: gpg2john invocations, gpg command invocations with real flags (--decrypt/--import/--export/--gen-key/-d/-k), BEGIN PGP PRIVATE KEY blocks, .gnupg/ + secring.gpg/pubring.gpg/trustdb.gpg paths, .gpg file operations, john --format=gpg / hashcat $gpg$ cracking; bare gpg/pgp words match far more files and a looser marker sweep collapses hard — bare-word and port/digit-adjacent matches adjudicated out in context)"
status: enriched
provenance: "corpus-mined from 9 strict-gated writeups in the writeup corpus (see ../internal coverage ledger) + upstream-verified: openwall/john gpg2john + gpg format (OpenPGP/GnuPG Secret Key, s2k-count cost flag), hashcat $gpg$ hash family (mode 17010 for the common S2K secret-key form, 16700-family symmetric OpenPGP), OpenPGP RFC 4880 packet/S2K structure (secret-key packets 5/7, S2K types 0/1/3, usage byte 254/255), GnuPG --pinentry-mode=loopback documentation. Accessed 2026-11-12"
---

# GnuPG (GPG / OpenPGP)

> An armored private key file is a credential store designed to be copied — the key material travels by email, lands in application databases, and gets committed to repos, while the passphrase is the only lock left standing once you hold it.

## What it is

GnuPG is the free implementation of OpenPGP: asymmetric keypairs where the secret key is itself encrypted under a passphrase via a string-to-key (S2K) derivation, plus a symmetric file-encryption mode used far beyond email — on engagements GPG most often appears as the encryption layer for *operational artifacts*: database dumps (`*.sql.gpg`), tar backups (`.tar.gz.enc`), password-manager stores, and root-only flag files. The per-user keyring lives in `~/.gnupg` (legacy GnuPG 1.x: `secring.gpg`/`pubring.gpg`; modern: `private-keys-v1.d/` key files plus `pubring.kbx`, `trustdb.gpg`). Keys exist in binary packet form or ASCII-armored text (`.asc`, `-----BEGIN PGP PRIVATE KEY BLOCK-----`), and the armored form is the same data base64-wrapped — which is why private keys surface in JSON columns, wiki pages, and chat exports.

The operator's mental model mirrors [[keepass]] and [[windows-secrets]] — sibling secret stores, different container: **steal key or ciphertext (or both), crack the passphrase offline, decrypt the loot**. The S2K derivation is deliberately expensive (iterated-and-salted, with counts in the tens of millions), so the cheap wins come before any cracking: passphrases reused from application configs, keys stored unprotected next to their data, and whole keyrings left world-readable.

The takeaway: on a host, GPG loot splits into three piles — keyrings (`~/.gnupg`), armored or binary key files (`.asc`, `.gpg` keys, password-manager key files), and ciphertexts (`*.sql.gpg`, `*.tar.gz.enc`, `*.gpg` anything). Each pile is separately stealable, and the exploitation question is always *which pile is incomplete*: ciphertext without its key sends you key-hunting; a key without its ciphertext is a passphrase-crack away from whatever it used to protect.

## The anatomy that matters on an engagement

    user → gpg keygen → ~/.gnupg/ (private-keys-v1.d/*.key, pubring.kbx, trustdb.gpg)
                          secret key = public part + S2K-encrypted private part
    ops → gpg --symmetric / --encrypt  →  *.sql.gpg, *.tar.gz.enc, root-flag.gpg  (backups, dumps, loot)
    apps → passpie (credential store, .keys + per-entry .gpg YAML) | passbolt (armored private key in a MySQL JSON column)

    attack A: steal keyring/key file → gpg2john → john/hashcat offline passphrase crack → import → decrypt
    attack B: steal ciphertext only → hunt the key (other host, app database, backup dir) → A
    attack C: steal key + find passphrase by reuse (app configs, DB connection strings, other vaults) → decrypt immediately
    attack D: key already imported on a host you shell → script the decrypt non-interactively (--pinentry-mode=loopback)

Three structural properties drive most findings:

1. **The key is designed to leave home.** Armored keys are text; they end up in ticketing systems, application databases (passbolt stores users' armored private keys alongside their passphrases' metadata), and dotfiles. Finding a `BEGIN PGP PRIVATE KEY BLOCK` string in any data dump is a key theft, not a curiosity.
2. **The passphrase is a user password, reused like one.** S2K makes guessing expensive per attempt, so the practical attack is dictionary-of-the-user, not raw brute force — application config files and database connection strings on the same host are the first wordlist.
3. **Keyrings are per-user but rarely permission-audited.** A `~/.gnupg` chmod'ed world-readable exposes `private-keys-v1.d` (the actual secret-key files) to any foothold user, converting a web-shell into offline key recovery without ever running gpg on the target.

## Where you'll meet it

- **Backup directories** — `/var/backups/` style paths holding `*.sql.gpg` database dumps or encrypted tars; the dump usually post-dates the credentials inside it, making decryption a lateral move.
- **User homes and dotfiles** — `~/.gnupg` keyrings, `.asc` files, and password-manager stores (passpie keeps a `.keys` file of GPG keys plus one small `.gpg` file per credential entry — a full credential store in two file types).
- **Application databases** — passbolt's schema carries users' armored private GPG keys; dump the table, de-armor, feed gpg2john.
- **Cross-host deployments** — a flag or backup encrypted on one machine whose keyring only exists on another; expect to reunite the halves across pivots.
- **PGP-flavored web applications** — verification/encryption services and key-upload workflows (a site that insists on GPG-signed uploads or offers to verify PGP messages); here the surface is the workflow (key handling, signature checking) rather than the secret store, and the bug class is the app's, not GnuPG's.

## Reading the implementation's mind

- `file` names armored keys "PGP secret key" / the `.asc` body carries the armor headers; binary keyrings open with the OpenPGP packet magic. Never trust the extension — `.keys`, extensionless dotfiles, and JSON-embedded blobs all carry real key material.
- **gpg2john → `$gpg$` hash → john `--format=gpg` or hashcat.** gpg2john parses secret-key packets (tags 5/7), skips GNU-dummy stubs (S2K type 101 — no secret material present, often the silent reason a "stolen key" won't crack), and emits the `$gpg$*…` hash with S2K parameters. John's `gpg` format reports the work factor as a cost flag (`s2k-count`, iterated-and-salted counts commonly in the tens of millions); hashcat's `$gpg$` family covers the same hashes (mode 17010 for the common S2K secret-key form; a 16700-family mode for symmetric-only OpenPGP data). Budget wordlists, not raw brute force.
- **Unprotected secret keys exist**: S2K usage byte 0 means no passphrase at all — import and use, no cracking. Always try a straight import-and-decrypt before investing in a crack.
- **Scripted decryption needs loopback**: interactive pinentry breaks automation; `gpg --batch --yes --pinentry-mode=loopback --passphrase <pw> -d file.gpg` is the non-interactive form for shells and scripts.
- **Legacy GnuPG 1.x keyrings port**: `secring.gpg`/`pubring.gpg` from a 1.4-era home are consumable by other OpenPGP implementations (netpgp on the BSDs reads a stolen `~/.gnupg` directly) — a stale keyring on an abandoned account is still a live secret store.
- **Keyless ciphertext is a hunt prompt**: encrypted backups sitting in a world-readable directory with no `.gnupg` on the host mean the key lives elsewhere — check application configs on the same host for the passphrase (file-encryption passphrases are routinely reused from database credentials), then other hosts for the keyring.

## Where to go next

[[gpg-bug-bounty|GnuPG bug bounty methodology]] — phased: locate the three piles (keyrings, keys, ciphertexts) → triage pairings → exfiltrate → offline passphrase cracking (gpg2john) → non-interactive decryption → GPG-backed stores (passpie, passbolt) and PGP-flavored web workflows.
