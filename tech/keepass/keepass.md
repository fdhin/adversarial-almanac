---
technology: KeePass
kind: overview
audience: offensive-security
family: credential-stores
layer: Password manager (KDBX container format, master password + keyfile unlock, memory-resident secrets)
language: C# (.NET) for KeePass 2.x; C for KeePass 1.x
first-released: 2003
homepage: "https://keepass.info"
evidence: "13 writeups (command-context gate: .kdbx operations, keepass2john incl. -k keyfile, kpcli, KeePassXC usage, CVE-2023-32784 / keepass-password-dumper, puttygen PPK conversions, KeePass process/dump references; all 6 parent seeds confirmed, gate adds 7)"
status: enriched
provenance: "corpus-mined from 13 writeups in the writeup corpus (see ../internal coverage ledger) + upstream-verified: vdohney/keepass-password-dumper README (CVE-2023-32784 mechanics, fixed in KeePass 2.54, dump sources, KeePass 1.x/KeePassXC/Strongbox unaffected, typed-not-pasted), openwall/john bleeding-jumbo src/keepass2john.c (KeePass 1.x+2.x, -k <keyfile>, KDBX4/Argon2 support), keepass.info release news 2.54. Accessed 2026-11-12"
---

# KeePass

> The database file is the crown jewel and it is designed to be stolen: one `*.kdbx` container holds every credential the user owns, and the master password — not the file — is the only thing worth attacking.

## What it is

KeePass (2003, Dominik Reichl) is the veteran open-source password manager: secrets live in an encrypted container file (`.kdb` in the 1.x line, `.kdbx` in 2.x, with KDBX4 adding Argon2 key derivation), unlocked by a master password, a key file (the KeePass XML key format or any arbitrary file such as an image), a Windows account binding, or a combination. KeePassXC is the cross-platform port that opens the same files; the CLI side is served by kpcli (interactive shell over a database) and keepass2john (hash extraction for offline cracking). On engagements the vault appears as a user's personal `*.kdbx` on a workstation, a shared vault on a file share or FTP site, a backup artifact, or an entry point hidden in unusual filesystem places (NTFS alternate data streams).

The operator's mental model: **crack the container offline, or catch the master password in flight**. The file alone is designed to survive theft — AES with per-database keys and a deliberately expensive KDF — so the working attacks aim elsewhere: the master password's fingerprints in process memory, the user's fingers on the keyboard while the vault is open, the key file sitting next to the database, or a password the user reused elsewhere.

The takeaway: a KeePass database found on a box is never a dead end, it is a commitment decision. Offline cracking and memory recovery are cheap to stage and run unattended; live keystroke capture and GUI interception pay immediately when a session is active; and the payoff is structured — every entry carries a URL, username, and password, so the vault usually contains the next hop's credentials, including SSH keys that need format conversion (PuTTY PPK to OpenSSH) before they are usable.

## The anatomy that matters on an engagement

    user → KeePass/KeePassXC process
      → master password typed into SecureTextBoxEx (2.x custom text box)
      → key = password [+ keyfile] [+ Windows account] → KDF (AES-KDF / Argon2 in KDBX4)
      → *.kdbx on disk (user profile, share, backup, ADS)

    attack A: steal the file → keepass2john → hashcat/john offline crack
    attack B: steal process memory → recover typed master password from leftover strings (CVE-2023-32784)
    attack C: capture live (keystroke logging while the process runs; screenshot/intercept the open GUI)
    attack D: keyfile recovery (XML key files, images, .key siblings of the database)
    attack E: unlocked-database exfiltration (exported entries, SSH keys in attachments)

Three structural properties drive most findings:

1. **The container is meant to be copied.** No server, no lockout, no telemetry — the format assumes offline attack, so theft is silent and unlimited-retry once you have the file (and the KDF is the only speed bump; KDBX4's Argon2 makes it a slow one).
2. **The master password transits memory and keyboard.** 2.x before 2.54 leaves per-character leftover strings in managed memory for every typed password; a single process dump recovers all but the first character regardless of lock state. Before that, the password exists on the wire from the user's fingers.
3. **Unlock factors live next to the secret.** Key files are stored as siblings of the database or chosen from memorable locations (photos, documents); an attacker with the directory usually has all factors except the password.

## Where you'll meet it

- **Administrator and developer workstations** — the personal vault of whoever runs the environment; often unlocked during the day, which makes live capture and GUI interception realistic.
- **File shares and FTP roots** — shared team vaults (`Shared.kdbx`-style names) with inherited readable permissions; the whole org's credentials in one file with weaker password discipline than a personal vault.
- **Backup and migration artifacts** — scheduled backups of user profiles, exfiltrated archives, databases hidden in NTFS alternate data streams attached to innocuous files.
- **Inside the vault: keys, not passwords** — entries frequently hold SSH private keys as attachments (PuTTY `.ppk` format on Windows), API tokens, and emergency credentials; format conversion (puttygen) is part of exploitation.

## Reading the manager's mind

- `file` on a real vault answers `KeePass password database 2.x KDBX` (or 1.x KDB) — never trust the extension; the container magic is distinctive either way.
- KDBX version decides crack cost: 1.x/KDBX3 use AES-KDF (fast on GPUs), KDBX4 uses Argon2 (memory-hard; budget accordingly before promising a crack). keepass2john in the jumbo John build handles 1.x, 2.x, and KDBX4, and `-k` folds a key file into the hash.
- CVE-2023-32784 (upstream history): KeePass 2.x before 2.54 types the master password into a custom text box that leaves a leftover string per character; a memory dump — process dump, pagefile, hibernation file, or full RAM, locked workspace or not — yields every character except the first. The password must have been typed (paste does not trigger it). KeePass 1.x, KeePassXC, and Strongbox are not affected.
- Key files do what users hope: keepass2john needs the key file (`-k`) to build the hash, so its absence — not just the password — can gate a crack. The KeePass XML key format wraps a random key in a small XML file; any arbitrary file can serve as a key file, so image/document siblings are worth testing.
- An unlocked vault on screen is a database dump: entry listings, and attachments exported without the master password. GUI session interception (accessing the logged-in user's desktop session) and process keystroke capture both harvest at this layer.

## Where to go next

[[keepass-bug-bounty|KeePass bug-bounty methodology]] — phased: locate databases → offline cracking (keepass2john, KDF cost model) → memory recovery (CVE-2023-32784) → live capture → key files → loot processing (kpcli, PPK conversion).
