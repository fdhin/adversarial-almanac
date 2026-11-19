---
name: "hashcat"
categories:
  - 802-11
  - passwords
version: "1:8.0.0-1"
homepage: "https://hashcat.net/hashcat"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-10-25"
build-system: make
license: MIT
meta-checked: "2026-11-09"
github-repo: "hashcat/hashcat"
github-stars: 27282
github-pushed: "2026-11-07"
github-archived: false
github-issues: 52
github-match: "search"
gh-checked: "2026-11-07"
worth: 9.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 707
gh-activity-checked: "2026-11-09"
---

# hashcat

> GPU-accelerated password recovery utility covering 700+ hash formats with dictionary, rule, mask, and hybrid attacks.

## What it's for

hashcat turns hashes you already have into plaintext: it drives OpenCL-capable GPUs (and CPUs) through a candidate generator and tests every candidate against a hash list at millions of guesses per second. It is the standard offline cracker for recovered credential material — Windows/Active Directory hashes (NTLM, NetNTLMv1/v2, Kerberos tickets), Linux shadow hashes, web-application KDF hashes (bcrypt, PBKDF2), document and archive encryption keys, and WPA/WPA2 handshakes converted to PMKID/EAPOL format. It does not capture or extract anything itself: hashes must be harvested with other tools and, where needed, converted into hashcat's expected formats ([[hashcat-utils]] ships several converters, e.g. cap2hccapx for packet captures; hcxdumptool/hcxpcapngtool produce the current WPA 22000 files). Attack modes cover straight dictionary, combination, mask brute-force, hybrid wordlist+mask, and rules-based mangling; found passwords accumulate in a potfile so re-runs skip solved hashes. Note the major version-7/8 interface changes: several long-standing hash-mode numbers were renumbered (see the slow-KDF use case below), the shipped best64.rule ruleset became best66.rule, and `-w/--workload-profile` was removed in favor of `-n/-u/-T` kernel tuning. Legacy distributions and older documentation through hashcat 6.x (including the old vendor-split oclHashcat/cudaHashcat editions — see [[oclhashcat]], [[cudahashcat]]) still use the old numbers. Menu-driven wireless tooling such as [[airgeddon]] shells out to hashcat for the offline cracking step.

## Typical use cases

### Crack a hash list with a straight dictionary attack

Test every word of a wordlist against the target hashes.

```
hashcat -m 1000 ntlm.txt /usr/share/wordlists/rockyou.txt
```

Outcome: hashcat loads ntlm.txt as NTLM (`-m 1000`) hashes, benchmarks the GPU/CPU backend, and streams rockyou.txt candidates against them, printing a live status screen (speed, progress, recovered count). Recovered password:hash pairs are written to the potfile and shown in green. `-a 0` (straight) is the default attack mode; omitting `-m` starts an autodetect run against the whole list, and hash lines that carry a username prefix need `--username` so the prefix is ignored.

### Identify the hash mode before attacking

Find the numeric `-m` code for an unfamiliar hash format.

```
hashcat --example-hashes | grep -i kerberos
```

Outcome: prints the built-in hash-mode catalog filtered to matching algorithms — each entry shows the mode number, its name, and a valid example hash, so you can match the target hash's structure and copy the number into `-m`. Through hashcat 6.x this table lived in `--help` output (the classic `hashcat --help | grep <algorithm>` idiom); version 7 moved it to `-H/--hash-info`, of which `--example-hashes` is a documented alias.

### Expand a wordlist with mangling rules

Apply best-practice transformations to each dictionary word before testing it.

```
hashcat -m 1800 hashes.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best66.rule
```

Outcome: every rockyou.txt candidate is additionally mutated by each of the rules in best66.rule (append digits, capitalize, leetspeak, duplicates), multiplying coverage of password-policy patterns against the sha512crypt (`-m 1800`) hashes without a bigger wordlist. Through hashcat 6.x the shipped ruleset file was named best64.rule — older references and guides use that path. To only generate the mangled candidates instead of cracking, add `--stdout` (e.g. `echo 'Password1' | hashcat -r /usr/share/hashcat/rules/best66.rule --stdout`), which prints every transformed candidate for use in any other checker.

### Brute-force a known password shape with a mask

Generate candidates that fit a known pattern instead of a full keyspace walk.

```
hashcat -m 1000 ntlm.txt -a 3 '?u?l?l?l?l?d?d' -i
```

Outcome: mask attack (`-a 3`) generates every candidate matching the pattern — uppercase letter, four lowercase letters, two digits (`?u?l?l?l?l?d?d`) — directly on the GPU without any wordlist. `-i/--increment` additionally grows the mask from its leftmost placeholders outward, trying all shorter shapes first (bound with `--increment-min`/`--increment-max`). A partially known plaintext can be encoded by fixing characters literally and masking the rest (e.g. `'Pass?a?a?a?a'`). Built-in charsets: `?l ?u ?d ?s ?h ?H ?a ?b`.

### Combine a wordlist with a mask tail (hybrid attack)

Append generated suffixes to each dictionary word.

```
hashcat -a 6 -m 1400 hashes.txt /usr/share/wordlists/rockyou.txt '?d?d?d?d?d?d' -o cracked.txt
```

Outcome: hybrid mode 6 pairs every rockyou.txt word with every six-digit suffix (`?d?d?d?d?d?d`) against the SHA-256 (`-m 1400`) hashes — the pattern for passwords that are a base word plus a date or counter. `-o cracked.txt` writes each recovered password:hash pair to a file as it is found. Mode `-a 7` inverts the pairing (mask prefix + wordlist word).

### Crack Kerberos tickets from Active Directory

Recover service-account passwords from pre-auth-free or roasted tickets.

```
hashcat -m 18200 asrep.txt /usr/share/wordlists/rockyou.txt -o cracked.txt
```

Outcome: attacks Kerberos 5 etype-23 AS-REP hashes (`-m 18200`, the output of AS-REP roasting tools invoked with `-format hashcat`), printing and potfile-recording any recovered service-account password, and writing findings to cracked.txt. Kerberoasted TGS-REP tickets use `-m 13100`; etype-18 pre-auth hashes use `-m 19900`. Find the right variant by comparing the `$krb5$...` hash prefix against `hashcat --example-hashes` output.

### Crack NetNTLM and MSSQL challenge-response hashes

Recover Windows network-authentication material captured off the wire.

```
hashcat -m 5600 netntlmv2.txt /usr/share/wordlists/rockyou.txt
```

Outcome: runs rockyou.txt against NetNTLMv2 hashes (`-m 5600`) harvested from responder/relay captures; a recovered value yields the user's password, which can be replayed against SMB and other services. NetNTLMv1/ESS variants use `-m 5500`. MSSQL database logins use `-m 131` (SQL Server 2000) and `-m 132` (2005/2008).

### Speed up salted slow hashes with optimized kernels

Trade candidate length for throughput on Unix and web KDF hashes.

```
hashcat -m 1800 hashes.txt /usr/share/wordlists/rockyou.txt -O
```

Outcome: `-O/--optimized-kernel-enable` switches from pure to optimized GPU kernels for hash modes that ship them (sha512crypt `-m 1800`, md5crypt `-m 500`, SHA-2 modes, and most fast hashes), substantially raising guess rate while capping the maximum candidate length. Not every mode has optimized kernels — bcrypt `-m 3200` and WPA `-m 22000` run pure kernels only. Important renumbering in hashcat 7+: generic PBKDF2-HMAC-SHA256 moved from `-m 10900` to `-m 40024`, Django (PBKDF2-SHA256) from `-m 10000` to `-m 40022`, Drupal7 from `-m 7900` to `-m 40020`, and Password Safe v3 from `-m 5200` to `-m 40011`; the old numbers are gone from v7/v8. The legacy `-w 3` workload flag no longer exists — use `-n/--kernel-accel`, `-u/--kernel-loops`, and `-T/--kernel-threads` for manual tuning.

### Crack WPA/WPA2 handshakes and PMKIDs

Attack WiFi pre-shared keys captured as PMKID/EAPOL material.

```
hashcat -m 22000 capture.22000 /usr/share/wordlists/rockyou.txt
```

Outcome: dictionary attack against the WPA PBKDF2 keys derived from the handshakes/PMKIDs converted into the combined 22000 format (produced by hcxpcapngtool); the AP's SSID is part of each hash line, so entries from multiple networks can be attacked in one run, and a recovered value is the WiFi passphrase. Legacy `-m 16800` files hold PMKID-only captures in the older format. Archive passwords use the same pattern: `-m 13600` for WinZip and the 17200–17230 family for PKZIP variants.

### Review and manage results with the potfile

Show which hashes are already cracked without re-running an attack.

```
hashcat -m 0 hashes.txt --show
```

Outcome: compares hashes.txt against the potfile and prints only the already-cracked hash:password pairs; no cracking happens. `--left` prints the inverse (uncracked hashes only). Found plaintexts persist across sessions in the potfile, so subsequent attacks automatically skip solved hashes; pass `--potfile-disable` to run without recording (e.g. when testing wordlists against a shared hash list without polluting results).

### Run a combination attack with two wordlists

Concatenate every word of one dictionary with every word of a second.

```
hashcat -a 1 -m 9800 office.hashes base-words.txt modifier-words.txt
```

Outcome: combination mode (`-a 1`) makes every candidate a word from the first dictionary immediately followed by a word from the second (`summer` + `2026` → `summer2026`) and tests them against old Microsoft Office SHA1+RC4 document encryption (`-m 9800`); the kernel column of `--example-hashes` marks modes supporting this attack with an `a1` kernel. Unlike the hybrid modes `-a 6`/`-a 7`, both inputs are wordlists, so both halves of the password come from real vocabulary instead of a mask.

### Crack a JWT signing secret

Recover the HMAC secret of a JSON Web Token.

```
hashcat -a 0 -m 16500 jwt.txt /usr/share/wordlists/rockyou.txt
```

Outcome: mode 16500 targets the token's HMAC-SHA256 signature — every candidate is tested as the HS256 signing key, and a recovered plaintext is the secret needed to forge arbitrary tokens. Feed one base64 token per line (`header.payload.signature`); rules apply as usual, e.g. `-r /usr/share/hashcat/rules/best66.rule`.

### Crack network protocol challenge-response secrets

Recover pre-shared keys from VoIP, VPN, and network-gear authentication captures.

```
hashcat -a 0 -m 11400 sip.hashes /usr/share/wordlists/rockyou.txt
```

Outcome: dictionary attack against SIP digest authentication (MD5) hashes converted from packet captures; a hit yields the SIP account password. The same pattern covers IKE pre-shared keys (`-m 5400` IKE-PSK SHA1, `-m 5300` MD5), TACACS+ (`-m 16100`), and iSCSI CHAP MD5 challenge-responses (`-m 4800`, the same MD5 construction EAP-MD5 captures use).

### Crack LUKS disk-encryption keys

Attack the passphrase of an encrypted volume from its header alone.

```
hashcat -m 14600 luks-header.img /usr/share/wordlists/rockyou.txt
```

Outcome: LUKS mode ingests a v1 header image (e.g. from `cryptsetup luksHeaderBackup --header-backup-file luks-header.img`) directly as the hash file, derives the PBKDF2 key for each keyslot, and reports the volume passphrase on a hit. Current hashcat names the mode `LUKS v1 (legacy)` — LUKS2 volumes are not covered.

### Crack timer-roasted and NT-input AD hashes

Attack the newest Active Directory roasting artifacts, including with NT-hash candidate lists.

```
hashcat -m 31300 timeroast.hashes /usr/share/wordlists/rockyou.txt --username
```

Outcome: mode 31300 (MS SNTP) cracks MS-NTP "timeroast" captures of a domain controller — a recovered candidate is the machine account password of the DC that answered. The companion `(NT)` modes take NT-hash candidates instead of passwords: `-m 31600` attacks Domain Cached Credentials 2 (DCC2 / MS Cache 2) with a list of NT hashes, and `-m 35300` attacks Kerberos 5 TGS-REP tickets the same way; `--username` keeps the per-line user prefix out of the guess.

### Crack DNSSEC NSEC3 hashes

Reveal hidden owner names in a DNSSEC-signed zone.

```
hashcat -m 8300 nsec3.hashes /usr/share/wordlists/rockyou.txt
```

Outcome: attacks NSEC3 next-owner hashes (SHA-1, salted, iterated) extracted from zone data; each input line carries the hashed owner name, the salt, and the iteration count (copy the exact field order from `--example-hashes`). A recovered plaintext proves that DNS name exists in the zone even though it is not resolvable — the check for zones that leak internal hostnames.

## Bug bounty workflow with hashcat

hashcat sits at the *end* of a chain, not the start. In a bug bounty it only ever runs against hashes you already obtained through an in-scope finding — a SQL-injection dump, an exposed backup, a misconfigured `.git`, a leaked config, a readable `/etc/shadow`, a captured NetNTLM response from a vulnerability you are authorized to exploit. The cracking itself is offline and local: it touches your GPU, never the target, so it raises none of the rate-limit/DoS concerns that gate active scanning. The scope question moves entirely to **how you got the hash** and **what you do with the plaintext**. Treat the phases below as a gate-first pipeline — confirm you may crack it, prove impact with the minimum effort, stop.

**Scope reality:** the offline, no-traffic-to-target nature of cracking is exactly why hunters over-reach with it. A dumped `users` table feels like free candy. It is not — see Phase 4 and the scope-discipline section. One cracked hash proves the impact of the disclosure bug; the other ten thousand are other people's credentials you now have to hold, secure, and delete.

### Phase 0 — Confirm you are allowed to crack this hash

Before any `-m`, answer three questions in writing (they go in the eventual report):

- **Did an in-scope vulnerability produce this hash material?** If the hash came from a finding outside scope, the finding is the problem, not the crack. If it came from a public breach corpus rather than the target, cracking it tells you nothing about *this* program and may violate its rules.
- **Does the program allow demonstrating credential impact at all?** Many programs want the *disclosure* reported and explicitly forbid logging in with recovered credentials. "I can read the hash DB" is the finding; "I logged in as admin" may be a scope violation even though the password was crackable.
- **Where will the hashes and any plaintexts live?** Local, encrypted, deleted on report closure. Never a third-party/online cracking service (that is disclosure of program data — see scope discipline).

### Phase 1 — Identify the hash mode

You cannot attack what you cannot classify, and the `-m` number decides both the kernel and the keyspace economics.

```
hashcat --identify target.hash
```

Outcome: hashcat 7+ prints every hash-mode whose structure matches the input (the modern replacement for the old `hashcat --help | grep` idiom). Narrow the list by eye using the prefix (`$2b$` → bcrypt 3200, `$6$` → sha512crypt 1800, `$argon2id$` → Argon2 34000, `$P$`/`$H$` → phpass 400, `header.payload.signature` → JWT 16500). Cross-check structure and field order against `hashcat -m <n> --example-hashes` (or `-H`/`--hash-info`, with `-HH` for the detailed per-mode view). You can also simply omit `-m` entirely and let hashcat autodetect at launch — but for a report you want the number pinned deliberately, not guessed at runtime.

**v8 renumbering, so your `-m` is reproducible:** hashcat 8.0.0 moved a block of "recipe" and slow-KDF modes into a new 40000–44000 range — PBKDF2-HMAC-SHA256 `10900 → 40024`, Django PBKDF2-SHA256 `10000 → 40022`, Drupal7 `7900 → 40020`, Password Safe v3 `5200 → 40011`, and others. The old numbers **still work with `-m`** (back-compat is preserved), but `--identify`, `--example-hashes`, `-HH`, and `--benchmark-all` emit only the new numbers. Put the exact hashcat version next to every `-m` in the report so a triager reproduces your run, not a differently-numbered one.

### Phase 2 — Benchmark before you commit

The single most useful habit: find out whether the hash is crackable in your time budget *before* burning hours. The mode determines everything.

```
hashcat -b -m 3200
```

Outcome: `-b/--benchmark` reports the raw guess rate for that mode on your hardware (a single modern GPU benchmarks NTLM in the hundreds of GH/s but bcrypt at cost 5 in only tens of kH/s — a difference of seven orders of magnitude). `--benchmark-all` sweeps every mode. For a planned attack rather than a raw mode, `--speed-only` returns the expected speed of *your actual command* then quits, and `--progress-only` returns the ideal step size and time to process. `--keyspace` prints the candidate count of a given attack so you can divide keyspace by speed and get a real ETA.

Turn that into a go/no-go decision: a fast unsalted hash (NTLM, raw MD5/SHA1) is worth a mask brute-force; a correctly-tuned bcrypt/scrypt/Argon2 hash is only worth a *targeted* dictionary+rules pass, because exhaustive search is infeasible and that infeasibility is itself the finding (the password policy is doing its job — say so).

### Phase 3 — Climb the attack ladder cheapest-first

Run attacks in increasing cost, stopping the moment you have what the report needs:

1. **Straight dictionary** (`-a 0`) against rockyou.txt and any target-specific wordlist (company name, product, local language). Cheapest, highest hit rate on real human passwords.
2. **Dictionary + rules** (`-r rules/best66.rule`, then larger rulesets like `dive.rule`) — models the mangling humans actually do.
3. **Association attack** (`-a 9`) when you have a hint *per hash* — the username, email, or an old password aligned line-for-line with the hash file. hashcat 8 can build candidates from usernames and other info tied to each hash, which nails "username as password," "Welcome + company," and reused-credential patterns that a global wordlist dilutes.
4. **Targeted masks** (`-a 3`) only when you know the password *shape* (a policy of "1 upper, lower, 2 digits, 1 symbol" is a mask, not a brute-force) — and only on fast hashes.
5. **Hybrid** (`-a 6`/`-a 7`) for base-word + suffix/prefix patterns, and **combination** (`-a 1`) for two-word passphrases.

`--stdout` lets you preview what any wordlist+rule combination actually generates before committing GPU time to it — pipe it to `wc -l` for the count or `head` for a sanity check.

### Phase 4 — Prove one, then stop

```
hashcat -m 3200 one.hash rockyou.txt -r rules/best66.rule --potfile-path ./engagement.pot -o cracked.txt
hashcat -m 3200 one.hash --show --potfile-path ./engagement.pot
```

Outcome: `--show` reprints the cracked `hash:plain` pair from the engagement-scoped potfile without re-running. For a bug bounty, **one** cracked credential from a leaked set is almost always enough to establish severity ("the exposed hash is a crackable password" → the disclosure is High, not Info). Cracking the entire dumped table buys you no additional severity and a large pile of liability (Phase 0, scope discipline). Use a per-engagement `--potfile-path` so results never bleed between clients or into your personal potfile.

### Phase 5 — Translate the crack into an honest finding

The report claim is not "I cracked it" — it is "a credential protected by *this* hashing scheme fell to *this* much effort." That framing requires work-factor honesty: an NTLM or raw-MD5 hash falling in seconds says almost nothing about the user's password, because *any* password of that length falls in seconds under that algorithm — the finding is the algorithm choice. A bcrypt-cost-12 or Argon2id hash falling to a dictionary attack is a genuinely weak *password*, because the work factor was doing its job and the password lost anyway. State which one you are claiming. Capture the benchmark, the command, the mode+version, and the time-to-crack (Reporting evidence).

## Where the money is with hashcat

hashcat does not find bugs; it converts a *disclosure* or *capture* bug into a demonstrated impact, which moves severity. The table is ordered by how often a cracked hash is the hinge of a bounty report.

| Priority | Finding class the crack supports | Mode(s) that reach it | Typical severity of the underlying bug | Over-reach / dupe risk |
|---|---|---|---|---|
| 1 | Leaked web-app credential store (SQLi dump, exposed backup/`.sql`, readable config) | bcrypt `3200`, phpass `400`, Django/PBKDF2 `40022`/`40024`, sha256/512crypt `7400`/`1800`, Argon2 `34000`, scrypt `8900` | High–Critical (the disclosure) | Crack **one** to prove impact; cracking the whole table is scope abuse |
| 2 | Weak/forgeable JWT HMAC secret | HS256 `16500` | High–Critical (token forgery) | Low dupe risk; a crackable secret = full auth bypass |
| 3 | Exposed Unix/Linux shadow or password file | sha512crypt `1800`, md5crypt `500`, sha256crypt `7400`, bcrypt `3200`, yescrypt (unsupported — note it) | High (the file disclosure) | One account is enough; do not crack every user |
| 4 | Captured Windows network auth (if MITM/relay is in scope) | NetNTLMv2 `5600`, NetNTLMv1 `5500` | High | Only if interception is explicitly authorized |
| 5 | Active Directory roasting (internal/red-team scope) | Kerberoast `13100`, AS-REP `18200`, timeroast `31300` | High | AD scope is rarely in public bounty programs |
| 6 | Exposed app-framework hashes (WordPress/Joomla/Drupal/htpasswd) | phpass `400`, bcrypt `3200`, Drupal7 `40020`, apr1 `1600` | Medium–High | Very common target; check for dupes |
| 7 | Document / archive / disk key disclosure | Office `9400`–`9800`, PDF `10400`–`10700`, ZIP/RAR/7z `13600`/`17200`+, LUKS `14600` | Medium–High | Only files obtained in scope |
| 8 | DB-engine login hashes in a dump | MySQL `300`/`200`, MSSQL `131`/`132`/`1731`, PostgreSQL `12` | Medium–High | Part of the same dump as priority 1 |
| 9 | Confirming password-policy weakness for a hardening report | any mode, via `-b` + `--keyspace` ETA | Low–Info (often informational) | Frame as work-factor evidence, not a "crack" |

The recurring pattern: the *bug* is the exposure (SQLi, IDOR to a backup, open bucket, `.git` leak). hashcat's job is to answer the triager's inevitable question — "so what if the hashes leaked, aren't they hashed?" — with a concrete yes. Priorities 4–5 belong to authorized internal engagements far more than to public web bounties; do not assume AD or network-capture techniques are in scope for a web program.

## Command recipes

Every recipe below is offline and sends nothing to the target, so the discipline is about *data handling and effort budget*, not rate limits. The low-cost settings are called out.

**Identify, then benchmark, then decide (always this order):**

```
hashcat --identify dump.hash                 # candidate modes for an unknown hash
hashcat -b -m 3200                            # raw bcrypt speed on this GPU
echo 'Password1' | hashcat -a 0 -m 3200 --keyspace rockyou.txt   # candidate count for ETA
```

Outcome: `--identify` lists matching modes; `-b` gives the mode's guess rate; `--keyspace` gives the attack's candidate total. `candidates ÷ speed = ETA` — if that exceeds your budget for a slow hash, stop and write the work-factor finding instead of grinding.

**Dictionary + rules, scoped potfile, written output (the workhorse):**

```
hashcat -m 3200 webapp.hash /usr/share/wordlists/rockyou.txt \
  -r /usr/share/hashcat/rules/best66.rule \
  --potfile-path ./engagement.pot -o cracked.txt --outfile-format 3
```

Outcome: runs rockyou mangled by best66 against the bcrypt hashes; `--outfile-format 3` writes `hash[:salt]:plain` to cracked.txt; `--potfile-path` isolates results to this engagement so nothing leaks between clients. `best66.rule` is hashcat 7's rename of the old `best64.rule` — older guides and distros still ship the 64 name.

**Preview candidates without cracking (`--stdout`):**

```
hashcat -a 0 --stdout wordlist.txt -r rules/best66.rule | wc -l
hashcat -a 3 --stdout '?u?l?l?l?l?d?d?d?d' | head
```

Outcome: `--stdout` prints the exact candidate stream a given attack would generate — no hash file, no GPU work. Use it to size a run before committing, to sanity-check a custom rule, or to feed candidates into another checker. `-a 3 --stdout` materializes a mask so you can confirm its shape and count.

**Model the program's password policy as a mask (fast hashes only):**

```
hashcat -m 1000 ntlm.hash -a 3 '?u?l?l?l?l?l?l?d?d?s' --increment --increment-min 6
```

Outcome: a mask attack shaped to a "one upper, lowers, two digits, one symbol, min length 6" policy. `--increment` grows the mask from the minimum length outward (`--increment-min`/`--increment-max` bound it). Reserve masks for fast modes (NTLM, raw MD5/SHA1) — on bcrypt/Argon2 the keyspace is infeasible and the attempt is wasted.

**Association attack — one hint per hash (`-a 9`):**

```
hashcat -a 9 -m 1000 user-aligned.hash hints.txt -r rules/best66.rule
```

Outcome: association mode pairs each line of `hints.txt` with the hash on the *same line* of the hash file (username, email local-part, old leaked password, company token), optionally mangled by rules. This is the precise tool for "username equals password" and reused-credential checks, and hashcat 8 extends it to build candidates from usernames tied to each hash. Far more efficient than diluting a global wordlist with per-user guesses.

**Crack a JWT HMAC secret (token forgery impact):**

```
hashcat -a 0 -m 16500 jwt.txt /usr/share/wordlists/rockyou.txt -r rules/best66.rule
```

Outcome: tests each candidate as the HS256 signing key for the token in `jwt.txt` (one `header.payload.signature` per line). A hit is the secret needed to forge arbitrary tokens — pair it with a forged admin token (only if the program allows demonstrating it) for a clear auth-bypass report.

**Review results and manage the hash list:**

```
hashcat -m 3200 webapp.hash --show --potfile-path ./engagement.pot --username
hashcat -m 3200 webapp.hash --left --potfile-path ./engagement.pot
```

Outcome: `--show` prints only the already-cracked `hash:plain` pairs (no cracking run); `--left` prints only the uncracked ones; `--username` strips a leading `user:` column so it is not treated as part of the hash (recommended alongside `--show` on `user:hash` dumps). `--potfile-disable` runs without recording at all — use it when you deliberately do not want plaintexts persisted.

**Long run with session control and a hard time cap:**

```
hashcat -m 1800 shadow.hash rockyou.txt -r rules/dive.rule \
  --session webapp-shadow --runtime 7200 --status --status-timer 30 \
  --potfile-path ./engagement.pot
```

Outcome: `--session` names the run so `--restore` can resume it after a `[q]uit` or crash; `--runtime 7200` aborts after two hours (a self-imposed effort budget); `--status`/`--status-timer` emit a periodic status screen you can screenshot for the report. `--machine-readable` and `--status-json` give parseable status if you are scripting evidence capture.

**Debug which rule cracked a hash (for a precise write-up):**

```
hashcat -m 0 md5.hash rockyou.txt -r rules/best66.rule \
  --debug-mode 4 --debug-file cracked-rules.txt
```

Outcome: `--debug-mode 4` records the original word, the rule that fired, and the resulting plain to `cracked-rules.txt` — so the report can show *exactly* how a candidate became the password (e.g. `password` + rule `c $1` → `Password1`), which is strong evidence that the password followed a predictable human pattern.

## Pitfalls, noise and scope discipline

hashcat sends no traffic, so the classic "you flooded us / you got banned" failure mode does not apply. The ways hunters get a hashcat-backed report **rejected, or get themselves into legal and ToS trouble**, are all about scope and data handling:

- **Cracking beyond the finding.** The disclosure of a hash store is the bug; cracking one credential proves impact. Cracking the whole table does not raise severity — it just means you now hold thousands of real people's passwords. Many programs treat mass-cracking of exfiltrated user data as a scope violation regardless of how you got the dump. Crack the minimum, document, delete.
- **Sending hashes to online/cloud crackers.** Services like web "hash lookup" sites and GPU-rental cracking portals mean uploading *the program's data to a third party*. That is an uncontrolled disclosure — frequently a hard rules-of-engagement breach and a privacy problem — even if it is faster. Crack locally. (This is also a `userEmail`-style privacy rule: never attach client identity to anything leaving your machine.)
- **Using the plaintext to log in.** A crackable hash and an actual authenticated session are two different actions. Unless the program explicitly permits credential validation, stop at "the hash cracks to `<redacted>`"; do not authenticate as the victim. When you must prove it, prefer a *test account you control* whose hash you cracked, not a real user's.
- **Potfile and wordlist bleed.** hashcat's default potfile persists every crack across all runs and clients. Without `--potfile-path`, a `--show` can surface another engagement's credentials into this report, or a stale crack can make you think you solved a hash you didn't. Use a per-engagement potfile and delete it on closure.
- **Dishonest work-factor claims.** Reporting "I cracked the admin password" when the algorithm was unsalted MD5 overstates the *password* weakness and understates the *algorithm* weakness — triagers notice, and it costs credibility. Be explicit about which is the finding (see below).
- **Wrong mode = wrong conclusion.** Guessing `-m` (or trusting autodetect for the report) can make a strong hash look uncrackable or a weak one look strong. Pin the mode with `--identify` + `--example-hashes` and record the hashcat version, since 8.0.0 renumbered many modes.
- **Reading a benchmark as the target's weakness.** A GPU cracking NTLM at hundreds of GH/s is a statement about NTLM, not about the user. Keep the benchmark in the "algorithm choice" part of the report, separate from any claim about password strength.
- **Keeping the data.** Hashes and plaintexts are sensitive PII. Store them encrypted at rest for the life of the report only, and securely delete (hash files, potfile, `cracked.txt`, debug files) when the report is resolved. Say in the report that you did so.

## Reporting evidence

A hashcat-backed finding needs enough for a triager to reproduce the classification and believe the impact — without you shipping them a pile of live credentials.

- **The source of the hash.** Which in-scope vulnerability produced it (request/response or steps), proving the crack is downstream of an authorized finding — this is what makes the whole thing in scope.
- **A redacted sample hash**, not the whole dump. One line, with the account identifier masked, showing the structure that justifies the mode.
- **Mode identification:** the `--identify` output (or the `--example-hashes` line you matched against) and the final `-m` number, plus the **exact hashcat version** (`hashcat --version`) because of the 8.0.0 renumbering.
- **The benchmark:** `hashcat -b -m <n>` output for the mode, establishing the per-hash work factor on commodity hardware. This is the core of the algorithm-strength argument.
- **The exact attack command**, including wordlist and ruleset, so the effort is reproducible — and the **time-to-crack** (from the status screen or `--runtime`).
- **Proof of the crack, redacted.** The `--show` line with the plaintext masked (`hash:Pa****rd1`) or its length/character-class only, unless the program explicitly wants the value. For "how," the `--debug-mode 4` line showing word + rule → plain is strong, non-sensitive evidence of a predictable pattern.
- **The honest severity framing.** State plainly which finding you are making: *algorithm weakness* ("passwords are stored as unsalted MD5; any password cracks in seconds — see benchmark") versus *password weakness* ("despite bcrypt cost 12, this credential fell to a 14M-word dictionary in 9 minutes"). The remediation differs (migrate the hashing scheme vs. enforce password policy / breached-password checks), so the distinction belongs in the report.
- **Data-handling note.** State that cracking was local, that no data went to third-party services, that you cracked the minimum needed to prove impact, and that all hash/plaintext material will be securely deleted on resolution.
- **Scope note for credentials.** If the program forbade login with recovered credentials, say explicitly that you did not authenticate — the report stands on the disclosure plus the crack, not on account takeover.

## References

- hashcat changelog (official, authoritative for v7/v8 flag and hash-mode changes): https://github.com/hashcat/hashcat/blob/master/docs/changes.txt
- hashcat(1) manual (option reference): https://dyn.manpages.debian.org/trixie/hashcat/hashcat.1
- hashcat v7.0.0 release notes / announcement (Argon2 mode 34000, `--identify`, autodetect, HIP/Metal, best64→best66): https://hashcat.net/forum/thread-13330.html
- hashcat project wiki and homepage: https://hashcat.net/wiki/
- hashcat GitHub repository: https://github.com/hashcat/hashcat
- Hack The Box — "hashcat 7 release: top new features" (association-attack and autodetect overview): https://www.hackthebox.com/blog/hashcat-7-release-top-new-features

*Research supplement added 2026-11-15, grounded in the hashcat 8.0.0 changelog (docs/changes.txt), the hashcat(1) manual, and the v7.0.0 release notes/announcement — flags and hash-mode numbers verified against those sources (model: claude-opus-4-8).*
