---
technology: GnuPG (OpenPGP)
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
family: credential-stores
evidence: "9 writeups (see ../internal coverage ledger)"
provenance: "corpus-mined from 9 strict-gated writeups in the writeup corpus + upstream-verified: openwall/john gpg format + gpg2john (OpenPGP/GnuPG Secret Key, s2k-count cost), hashcat $gpg$ modes (17010 S2K secret-key form; 16700-family symmetric OpenPGP), RFC 4880 OpenPGP packet structure (tags 5/7, S2K types 0/1/3, usage byte 254/255, GNU-dummy 101), GnuPG --pinentry-mode=loopback. Accessed 2026-11-12"
---

Companion page: [[gpg|GnuPG overview]] — the keyring-as-secret-store model and where key material lives. Sibling stores: [[keepass]] (KDBX containers) and [[windows-secrets]] (DPAPI/LSA/registry stores) — same lifecycle, different containers. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# GnuPG Bug Bounty Methodology

**Scope:** GPG as a loot object and decryption target — locating keyrings, keys, and ciphertexts; offline passphrase cracking; non-interactive decryption; GPG-backed credential stores (passpie, passbolt); PGP-mandating application workflows. **Out of scope:** signing-infrastructure attacks (keyserver poisoning, web-of-trust), cryptographic breaks of OpenPGP itself, and the cracking tools' internals (tools vault).

**Authorization:** presume written permission. Keyrings and decrypted dumps are strictly need-to-know loot; cracked passphrases are credentials of real people — handle per engagement rules.

## Decision tree (one screen)

1. `~/.gnupg` or `.asc`/key file found → Phase 2 (triage) → Phase 4 (crack) or direct import if unprotected.
2. `*.gpg`/`.tar.gz.enc` ciphertext found, no key on host → Phase 1b (key hunt) — app configs for the passphrase first, other hosts' keyrings second.
3. Credential-manager artifacts (passpie `.keys`, passbolt DB) → Phase 6.
4. Web app demanding GPG signatures / offering PGP verification → Phase 7.
5. Key + passphrase in hand → Phase 5 (decrypt everything pairing to that key).

## Phase 1 — Locating the three piles

```bash
# keyrings
ls -la /home/*/.gnupg/ 2>/dev/null; find / -type d -name ".gnupg" 2>/dev/null
# key files and ciphertexts
find / \( -name "*.asc" -o -name "*.gpg" -o -name "*.pgp" -o -name "*.keys" -o -name "*.tar.gz.enc" -o -name "*.enc" \) 2>/dev/null | grep -vE "node_modules|/proc"
# armored keys hiding inside data (dumps, tickets, notes)
grep -rl "BEGIN PGP PRIVATE KEY BLOCK" /home /var /srv /opt /tmp 2>/dev/null
# typical habitats
ls -la /var/backups/ /home/*/backups/ /srv/backup* 2>/dev/null
```

Record permissions as you go: a world-readable `.gnupg` (or world-readable `private-keys-v1.d`) means every secret-key file inside is exfiltratable by the current user, whatever the home directory's intent was.

## Phase 2 — Triage: what pairs with what

```bash
file loot.gpg loot.asc                    # "PGP message" vs "PGP secret key" vs keyring
gpg --list-packets ciphertext.gpg 2>&1 | head    # key ID that would decrypt, without owning it
gpg --show-keys suspect.asc               # public-only or private?
```

Build the pairing map: which ciphertext names which key ID; which key file holds which key ID; which pile is missing. An encrypted dump whose key ID matches no keyring you hold routes to Phase 1b (hunting that key ID across hosts, databases, and backups) — not to cracking what you already have.

## Phase 3 — Exfiltration

```bash
tar czf /tmp/gnupg.tgz /home/victim/.gnupg            # whole keyring: private-keys-v1.d + pubring + trustdb
# or single files; armored keys copy as text
base64 -w0 key.asc > exfil.txt
```

Take the entire `~/.gnupg`, not just `secring`-shaped files — modern GnuPG stores each secret key as its own file in `private-keys-v1.d/` and gpg2john wants exactly those (or the armored equivalent). `random_seed` and revocation certificates are low value; skip them when stealth matters.

## Phase 4 — Offline passphrase cracking

```bash
gpg2john armored_key.asc > key.hash       # works on armored, binary, and keyring-derived keys
# quick sanity: does the key even have a passphrase? usage byte 0 = unprotected → skip to Phase 5
john key.hash --wordlist=/usr/share/wordlists/rockyou.txt --format=gpg
# hashcat path (17010 = the common S2K secret-key form; 16700-family for symmetric-only OpenPGP)
hashcat -m 17010 key.hash /usr/share/wordlists/rockyou.txt
```

Order of attack (the S2K work factor makes guesses expensive — spend them well):

1. **Reused credentials from the same host** — database connection strings in app source, `.bash_history`, other config files. File-encryption passphrases are reused at high rates; this is the fastest real-world win.
2. Organization-targeted wordlists (hostnames, project names, years) with best64-style rules.
3. Raw rockyou — background it; john reports the cost (`s2k-count`) per hash, so estimate before promising.

If john reports the hash loaded with **no** secret material or the key parses as a stub (GNU-dummy, S2K type 101), the private key is absent by design — stop, this is not a crackable artifact.

## Phase 5 — Non-interactive decryption

```bash
gpg --import recovered.key                                  # or import the whole keyring tarball
gpg --list-secret-keys                                      # confirm arrival + key ID
gpg --batch --yes --pinentry-mode=loopback --passphrase 'RECOVERED_PW' -d backup.sql.gpg > backup.sql
```

Loopback mode is mandatory for shells and scripts — interactive pinentry fails or hangs in non-TTY contexts. Batch-decrypt every ciphertext the pairing map tied to the key: dumps (`*.sql.gpg` → SQL to mine for credentials), encrypted tars (decompress after decrypt), and single files (flag/secret files). Decrypted database dumps feed the credential-reuse loops of every other unit.

## Phase 6 — GPG-backed credential stores

- **passpie** — a directory of one `.gpg`-encrypted YAML file per credential plus a `.keys` file containing the GPG key(s). The whole store is two file types: exfiltrate both, crack the `.keys` passphrase (Phase 4), then decrypt each entry (`passpie export` or straight gpg) for structured username/password/URL rows — the same loot shape as a [[keepass]] database.
- **passbolt** — server-side password manager whose MySQL schema stores each user's **armored private GPG key** (and its passphrase) in the database. With database read access, dump the users/key tables, de-armor the key (or feed the stored blob to gpg2john directly — armor is just base64 wrapping), crack or take the stored passphrase, then decrypt the secrets table entries client-side equivalent.

## Phase 7 — PGP-mandating application workflows

When the *application* itself speaks PGP (verification services, encrypted-message portals, upload flows requiring detached signatures):

- Enumerate the workflow as the user experiences it: keypair generation, public-key upload (`gpg --gen-key`, `gpg --export -a "name" > public.asc`), detached signing (`gpg -u name --detach-sign file`), message encrypt/verify endpoints.
- Attack surface is the app's handling, not GnuPG's crypto: signature-verification bugs (accepting unsigned/modified input, verification-result confusion — check whether a failed verify still processes the payload), template injection through message content, and trust placed in uploaded keys.
- The keypair you generate for the workflow is your own tooling — but the app's *stored* keys and passphrases (server configs, databases) are Phase 1 loot like any other.

## Appendix A — Tool map (tools vault owns per-tool detail)

- **gpg2john** — OpenPGP secret-key to `$gpg$` hash extraction (armored, binary, keyring files).
- **John the Ripper** — `--format=gpg`, reports `s2k-count` cost; wordlist + rules workflow.
- **hashcat** — `$gpg$` family (mode 17010 S2K secret-key form; 16700-family symmetric OpenPGP).
- **GnuPG (gpg/gpg2)** — `--list-packets`, `--show-keys`, `--import`, `--list-secret-keys`, batch loopback decryption.
- **passpie / passbolt** — GPG-backed credential stores; their storage layouts are in Phase 6.
- **netpgp** — BSD OpenPGP tooling that reads a stolen GnuPG keyring directly (legacy `secring.gpg` compatibility).
