---
name: "john"
categories:
  - passwords
  - top10
version: "1.9.0.jumbo1-13"
homepage: "https://www.openwall.com/john"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-01-31"
build-system: autotools
license: GPL2, custom
meta-checked: "2026-11-09"
github-repo: "openwall/john"
github-stars: 13880
github-pushed: "2026-11-04"
github-archived: false
github-issues: 524
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 111
gh-activity-checked: "2026-11-09"
---

# john

> Fast, multi-mode password cracker for hundreds of hash and ciphertext formats.

## What it's for

John the Ripper (the community "jumbo" build) cracks password hashes offline:
Unix crypt(3) variants, NTLM, raw MD5/SHA-family digests, bcrypt, and hundreds
of application formats — encrypted archives (ZIP, 7z, RAR), PDF and Office
documents, SSH private keys, GPG keyrings, KeePass databases, SQL and LDAP
server hashes. passwd/shadow-style input loads directly; most other formats
are first extracted with the bundled `*2john` converters (zip2john, ssh2john,
gpg2john, keepass2john, ...) whose output is fed to john. Cracked passwords
accumulate in the pot file (`john.pot`) so later runs skip them. It does not
attack live network services — that is the job of online brute forcers.

## Typical use cases

### Run a wordlist attack against extracted hashes

Crack a converter-produced hash file with a wordlist.

```
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

Outcome: autodetects the hash type in `hash.txt` (for example zip2john or
ssh2john output), tries every wordlist entry, prints each cracked password
live, and stores results in john.pot so a re-run finishes instantly.

### Force the hash format

Override autodetection when the input is ambiguous or loads the wrong type.

```
john --wordlist=/usr/share/wordlists/rockyou.txt --format=raw-sha512 hash.txt
```

Outcome: treats every line of `hash.txt` as the named format and cracks only
those hashes. Common values include nt, bcrypt, raw-md5, raw-sha256, 7z, gpg,
keepass and md5crypt-long; `john --list=formats` enumerates everything the
build supports.

### Audit a Unix password file

Merge passwd and shadow, then crack the combined file.

```
unshadow /etc/passwd /etc/shadow > hashes.txt
john hashes.txt
```

Outcome: `unshadow` (bundled with john) merges the two files into crackable
passwd-style lines; `john` then runs its default cascade — "single crack" mode
on account data first, then wordlist with rules, then incremental — reporting
each recovered login as it falls.

### Apply word-mangling rules

Multiply a wordlist with rule-based mutations.

```
john --wordlist=/usr/share/wordlists/rockyou.txt --rules hash.txt
```

Outcome: every wordlist entry is additionally mangled by the default ruleset
(case toggles, suffixes, substitutions); `--rules=Best64` or any custom
`[List.Rules:X]` section in john.conf selects an alternate ruleset via
`--rules=X`.

### Try a pattern with mask mode

Generate candidates from a mask when the password shape is known.

```
john --mask='?l?l?l?l?l?d?d?d' --min-len=6 --max-len=9 hash.txt
```

Outcome: enumerates exactly the mask's keyspace (?l lower-case, ?u upper-case,
?d digit, ?s special, ?a printable ASCII) between the length bounds, iterating
lengths incrementally; stacking `--mask='?w?d?d?d'` on a `--wordlist` run
instead appends the pattern to every word.

### Exhaust the keyspace with incremental mode

Keep cracking after wordlists run dry.

```
john --incremental=ASCII hash.txt
```

Outcome: tries all candidates ordered by character frequency from charset
files, shortest first, with no wordlist at all; `--incremental=digits`
restricts to numerics, and `--make-charset=custom.chr` builds a new charset
from already-cracked passwords for a targeted `--incremental=custom` follow-up
run.

### Show and filter cracked results

Report what has been cracked so far.

```
john --show hash.txt
```

Outcome: lists `password` lines for every hash in `hash.txt` that is already
cracked (reading john.pot) plus a summary of how many remain; `--show=left`
lists the still-uncracked hashes, and filters such as `--users=0` (UID-0
accounts only) or `--shells=-expired` narrow the report.

### Parallelise across cores and manage long sessions

Use several processes and resumable sessions for big jobs.

```
john --wordlist=/usr/share/wordlists/rockyou.txt --fork=4 --session=webapp hash.txt
```

Outcome: forks four processes that split the keyspace (each logs its own
progress); `john --restore=webapp` continues an interrupted session,
`john --status` reports progress of a running or interrupted one, and
`--pot=/path/file.pot` isolates this job's cracked-password store from the
default john.pot.

### Generate a mutated wordlist without cracking

Use john's rule engine as a candidate generator for any other tool.

```
john --wordlist=words.txt --rules --stdout > w_mutated.txt
```

Outcome: `--stdout` prints every rule-mangled candidate instead of cracking with it — `--rules=all` stacks every configured ruleset for maximum coverage, `--stdout=8` caps candidates at 8 characters, and piping through the bundled `unique` utility deduplicates the stream (`... | unique mangled.lst`). Feed the result to another checker or service that cannot apply rules itself.

### Crack a JWT's HS256 signing secret

Recover the secret behind a JSON Web Token's signature.

```
john jwt.txt --wordlist=/usr/share/wordlists/rockyou.txt --format=HMAC-SHA256
```

Outcome: the HMAC-SHA256 format ingests JWT lines directly — each line is the token's first two base64 segments with their dot (`header.payload`), a `#`, then the signature as hex; every candidate word is tried as the HMAC key and a hit is the token-signing secret (the format's self-tests include JWT vectors). Convert the raw token to that line shape first — a bare `john jwt.txt` will not autodetect it, which is why `--format` is required.

## Bug bounty workflow with john

Like [[hashcat]], john sits at the *end* of a chain, not the start. In a bug bounty it only runs against hashes you already obtained through an in-scope finding — a SQL-injection dump, an exposed backup or `.sql`, a readable `/etc/shadow`, a leaked SSH key or encrypted archive, a captured network-auth response you were authorized to collect. The cracking is offline and local: it touches your CPU/GPU, never the target, so it raises none of the rate-limit/DoS concerns that gate active scanning. The scope question moves entirely to **how you got the hash** and **what you do with the plaintext**. Treat the phases below as a gate-first pipeline — confirm you may crack it, prove impact with the minimum effort, stop.

**john and hashcat are complements, not rivals.** Run both; reach for each where it wins. The offline, no-traffic nature of cracking is exactly why hunters over-reach with it — a dumped `users` table feels like free candy. It is not (see Phase 4 and scope discipline). One cracked hash proves the impact of the disclosure bug; the other ten thousand are other people's credentials you now have to hold, secure, and delete.

### When to reach for john over hashcat

- **Unix/shadow context.** john reads `passwd`/`shadow`/pwdump-style input natively and autodetects the format. `unshadow /etc/passwd /etc/shadow` merges the two files so john has usernames, GECOS/full-name, UID, and shell for *every* account — which drives single-crack mode and lets you filter the report with `--users`, `--groups`, `--shells`, and `--salts`. hashcat cracks the bare hash; it cannot ingest a shadow file or exploit account metadata.
- **"Single crack" mode.** `--single` builds candidates from each account's login name, GECOS fields, and home-directory name, applying a heavy mangling ruleset — the single highest-ROI first pass on a shadow dump, nailing "username as password", "FirstnameLastname", and company-derived passwords with no wordlist at all. hashcat's association attack (`-a 9`) is the nearest analog but needs manual line-alignment.
- **CPU-only boxes.** john runs anywhere without OpenCL — a jump host, a laptop with no usable GPU — and many slow or oddball formats run fine on CPU.
- **Format breadth via `*2john`.** The jumbo build ships ~70+ converter programs in `/usr/lib/john/` (mirrored in `/usr/bin/`): `ssh2john`, `gpg2john`, `zip2john`, `rar2john`, `7z2john.pl`, `pdf2john.pl`, `office2john`, `keepass2john`, `bitlocker2john`, `luks2john`, `truecrypt2john`, `pfx2john`, Kerberos (`krb2john`, `kirbi2john`, `ccache2john`, `kdcdump2john`), wallets (`bitcoin2john`, `ethereum2john`, `electrum2john`, `monero2john`), `keychain2john`, `lastpass2john`, `bitwarden2john`, and dozens more. Autodetection then loads the converter output directly.
- **Oddball and legacy formats.** descrypt (traditional DES `crypt(3)`), bsdicrypt, and a long tail of application formats load and autodetect without a mode number; Kerberos AS-REP/TGS-REP crack with `--format=krb5asrep` / `--format=krb5tgs`.
- **The dynamic compiler.** `--format=dynamic=...` expresses an arbitrary hash construction (nested MD5/SHA with salts, e.g. `md5(md5($p).$s)`) on the command line — useful for a bespoke app hash neither tool ships a dedicated mode for. Enumerate what the build understands with `--list=subformats`.
- **Reach for hashcat instead** when raw GPU throughput on a fast unsalted hash is the bottleneck (NTLM, raw-MD5 mask brute-force) — it is far faster there. Use john for the Unix-context work, the conversion ecosystem, and CPU-only situations.

### Phase 0 — confirm you are allowed to crack this hash

Before any `--format`, answer three questions in writing (they go in the eventual report):

- **Did an in-scope vulnerability produce this hash material?** If the hash came from a finding outside scope, the finding is the problem, not the crack. A public breach corpus is not *this* target and cracking it may violate program rules.
- **Does the program allow demonstrating credential impact at all?** Many programs want the *disclosure* reported and explicitly forbid logging in with recovered credentials. "I can read the hash DB" is the finding; "I logged in as admin" may be a scope violation even though the password was crackable.
- **Where will the hashes and plaintexts live?** Local, encrypted, deleted on report closure. Never a third-party/online cracking or "hash lookup" service — that is disclosure of program data (see scope discipline). john's pot file defaults to `$JOHN/john.pot`; set `--pot=./engagement.pot` so results never bleed between clients.

### Phase 1 — identify and load the hash

You cannot attack what you cannot classify, and for a report you want the format pinned deliberately, not guessed at runtime.

```
john --list=formats                 # every format this build supports
john --list=format-details          # same list with field order / details
john --show=types hash.txt          # what john thinks each input line is
```

Outcome: `--list=formats` enumerates the jumbo build's full catalog (hundreds of formats); `--list=format-details` / `--list=format-all-details` add field order and attributes; `--list=subformats` covers the dynamic subformats. Match the prefix by eye (`$2b$`/`$2y$` → bcrypt, `$6$` → sha512crypt, `$5$` → sha256crypt, `$1$` → md5crypt, a 13-char string → descrypt, `$P$`/`$H$` → phpass, `header.payload.signature` → HMAC-SHA256/JWT), then pin it with `--format=NAME`. `--show=types` confirms the detected type and `--show=invalid` lists lines that fail the format's `valid()` check (a truncated or mis-converted line).

Prefix-to-format triage, for pinning `--format` by eye (always cross-check against `--list=format-details`):

| On the wire | Likely `--format=` | Notes |
|---|---|---|
| `$2a$`/`$2b$`/`$2y$` | `bcrypt` | Slow; `--costs=` to split by work factor |
| `$6$...` | `sha512crypt` | Standard modern Linux shadow |
| `$5$...` | `sha256crypt` | |
| `$1$...` | `md5crypt` | Legacy Linux/BSD; `md5crypt-long` for >15-char salts |
| 13 printable chars, no `$` | `descrypt` | Traditional DES `crypt(3)`; fast, DES-weak |
| `$P$`/`$H$` | `phpass` | WordPress/phpBB/Joomla |
| 32 hex chars | `raw-md5` **or** `nt` | Disambiguate with `--show=types`; NT is the Windows one |
| 40 / 64 hex chars | `raw-sha1` / `raw-sha256` | |
| `$krb5asrep$23$...` | `krb5asrep` | AS-REP roast |
| `$krb5tgs$23$...` | `krb5tgs` | Kerberoast (`-aes128`/`-aes256` variants exist) |
| `$sshng$...`, `$gpg$...`, `$zip2$...`, `$office$...` | autodetect | Converter-produced; let autodetect load them |
| `header.payload` + `#` + sig | `HMAC-SHA256` | JWT HS256 (see the JWT use case above) |

For Unix input, build full account context first:

```
umask 077
unshadow /etc/passwd /etc/shadow > hashes.txt
```

For application files, run the matching converter and feed its output — never point john at the original file:

```
ssh2john id_rsa > ssh.hash       # or zip2john / 7z2john.pl / office2john / pdf2john.pl ...
```

Treat any downloaded target file as untrusted: extract it in its own empty directory and pass paths as arguments. Record the exact john version next to the format in the report (the banner `john` prints at startup, or jumbo's `--list=build-info`) — jumbo vs. core and the build date decide what a triager can reproduce.

### Phase 2 — benchmark and budget (and the cost of incremental)

Find out whether the hash is crackable in your time budget *before* burning hours. The format determines everything.

```
john --test --format=bcrypt        # self-test, then benchmark c/s on this hardware
john --test=0 --format=bcrypt      # quick self-test only, no timing
```

Outcome: `--test` runs the format's self-tests then reports the raw guess rate in crypts/second. For salted formats john prints **two** numbers — "Many salts" (real throughput when many unique salts share work) and "Only one salt" (the virtual single-salt rate); on a large salted dump the "Many salts" figure is your real rate, and it is worth capturing. A modern CPU benchmarks raw-MD5/NT in the tens-to-hundreds of MH/s but bcrypt at cost 12 in only hundreds of c/s — orders of magnitude apart.

Turn that into a go/no-go decision: a fast unsalted hash (NT, raw-MD5/SHA1, descrypt) is worth a mask or incremental run; a correctly-tuned bcrypt/yescrypt/argon2 hash is only worth a *targeted* dictionary+rules pass, because exhaustive search is infeasible and that infeasibility is itself the finding (the password policy is doing its job — say so). `--costs=` filters salts by tunable cost so you crack the cheap salts first and skip the expensive ones.

**Incremental cost.** `--incremental[=MODE]` is john's Markov-ordered brute force: it tries candidates by descending per-position character frequency, shortest-first, and **in practice never terminates** on a full charset. It is a background/last-resort mode, not a go-to. Always bound it — `--max-run-time=3600`, `--max-candidates=N`, or a narrow charset (`--incremental=Digits`, `--incremental=Lower`). Reserve it for fast formats. The predefined modes (each backed by a shipped `.chr` file, most trained up to length 13):

| `--incremental=` | Keyspace | When |
|---|---|---|
| `ASCII` | all 95 printable ASCII (default) | widest, slowest |
| `LM_ASCII` | ASCII tuned for LM | LM hashes (halves ≤7) |
| `Alnum` | 62 letters+digits | typical policy space |
| `Alpha` | 52 letters | |
| `LowerNum` / `UpperNum` | 36 (one case + digits) | case-folding or case-known |
| `LowerSpace` | 27 (lowercase + space) | passphrase-ish |
| `Lower` / `Upper` | 26 letters | |
| `Digits` | 10 digits (trained to length 20) | PINs, numeric codes |

A `--make-charset` run (Phase 3 recipes) builds a `.chr` from your own cracked set for a biased `--incremental=Custom` follow-up.

### Phase 3 — climb the attack ladder cheapest-first

Run in increasing cost, stopping the moment you have what the report needs. Invoked bare (`john hash.txt`), john's default "batch mode" already cascades single-crack → wordlist+rules → incremental; for a controlled hunt, drive the stages explicitly:

1. **Single crack** (`--single`, default `[List.Rules:Single]`) — account-metadata candidates, cheapest and highest hit rate on shadow/unshadow input.
2. **Straight wordlist** (`--wordlist=rockyou.txt`) plus target-specific words (company name, product, local language).
3. **Wordlist + rules** — `--rules` applies the default `[List.Rules:Wordlist]` set; heavier configured sets include `--rules=best64`, `--rules=Jumbo` (combines the Wordlist, Single, Extra, and NT rules), `--rules=KoreLogic`, and `--rules=All` to stack every configured ruleset. Section names map to `[List.Rules:*]` in john.conf (matched case-insensitively); confirm the exact names your build installs. Add a second mangling pass with `--rules-stack=...`.
4. **Mask** (`--mask=...`, fast formats only) — placeholders `?l ?u ?d ?s ?a ?b ?h ?H`, user-defined `?1`–`?9` via `-1=?l?u`, bounded with `--min-length`/`--max-length`. A known password shape ("1 upper, lowers, 2 digits") is a mask, not a brute-force.
5. **Hybrid mask on a wordlist** — `--wordlist=words.txt --mask='?w?d?d?d'`, where `?w` is the parent word, so each word gets a three-digit tail (`?W` toggles case). This is john's equivalent of hashcat's `-a 6`/`-a 7`; masks stack onto wordlist/rules (the full chain is wordlist → rules → mask).
6. **Loopback** (`--loopback`) — feeds already-cracked pot passwords back as the wordlist, catching reuse and per-user variations across the set.
7. **Incremental last**, bounded (Phase 2).

`--stdout` previews what any stage actually generates before you spend time on it:

```
john --wordlist=rockyou.txt --rules=All --stdout | wc -l   # candidate count for an ETA
john --mask='?u?l?l?l?l?d?d?d' --stdout | head              # sanity-check a mask
```

### Phase 4 — prove one, extract with `--show`, then stop

```
john --wordlist=rockyou.txt --rules=best64 --pot=./engagement.pot one.hash
john --show --pot=./engagement.pot --format=bcrypt one.hash
```

Outcome: `--show` reads the pot and prints the cracked `user:password` lines plus a count of how many remain; `--show=left` prints only the uncracked hashes. For a bug bounty, **one** cracked credential from a leaked set is almost always enough to establish severity ("the exposed hash is a crackable password" → the disclosure is High, not Info). Cracking the entire dumped table buys no additional severity and a large pile of liability (Phase 0, scope discipline). On shadow input, narrow the report to what matters — `--show --users=0` (UID-0 accounts), `--show --groups=0,1`, `--show --shells=-expired` — rather than dumping every recovered login. Use a per-engagement `--pot=` so results never bleed between clients or into your personal `john.pot`.

### Phase 5 — translate the crack into an honest finding

The report claim is not "I cracked it" — it is "a credential protected by *this* hashing scheme fell to *this* much effort." That requires work-factor honesty: a descrypt, NT, or raw-MD5 hash falling in seconds says almost nothing about the user's password, because *any* password of that length falls in seconds under that algorithm — the finding is the algorithm choice. A bcrypt-cost-12 or yescrypt/argon2 hash falling to a dictionary is a genuinely weak *password*, because the work factor was doing its job and the password lost anyway. State which one you are claiming. Capture the `--test` benchmark, the exact command, the format+version, and the time-to-crack.

## Where the money is with john

john does not find bugs; it converts a *disclosure* or *capture* bug into demonstrated impact, which moves severity. The table is ordered by how often a cracked hash is the hinge of a bounty report.

| Priority | Finding class the crack supports | Format / feature that reaches it | Typical severity of the underlying bug | Over-reach / dupe risk |
|---|---|---|---|---|
| 1 | Exposed Unix shadow/passwd file | `unshadow` + autodetect (sha512crypt, sha256crypt, md5crypt, descrypt, bcrypt) + `--single` | High (file disclosure) | One account proves it; do not crack every user |
| 2 | Leaked web-app credential store (SQLi dump, backup, readable config) | bcrypt, phpass, Django, raw-sha256/512, scrypt — or the `dynamic=` compiler for a custom scheme | High–Critical (the disclosure) | Crack **one**; cracking the whole table is scope abuse |
| 3 | Weak/forgeable JWT HMAC secret | `--format=HMAC-SHA256` | High–Critical (token forgery) | Low dupe risk; a crackable secret = auth bypass |
| 4 | Disclosed SSH/GPG/PKCS#12 private key passphrase | `ssh2john` / `gpg2john` / `pfx2john` → autodetect | High | The key disclosure is the bug; crack to confirm it is usable |
| 5 | Encrypted archive / document disclosure | `zip2john`, `rar2john`, `7z2john.pl`, `pdf2john.pl`, `office2john` | Medium–High | Only files obtained in scope |
| 6 | Disk / volume key (header disclosure) | `luks2john`, `bitlocker2john`, `truecrypt2john` | Medium–High | Header must be obtained in scope |
| 7 | Password-manager / wallet database | `keepass2john`, `lastpass2john`, `bitwarden2john`, `bitcoin2john`/`ethereum2john` | High | Large liability; crack the minimum to prove it |
| 8 | Active Directory roasting (internal/red-team scope) | `krb5asrep` / `krb5tgs` via `kirbi2john`/`krb2john`/`ccache2john` | High | AD is rarely in public bounty scope |
| 9 | App-framework hashes (WordPress/Joomla/htpasswd) | phpass, bcrypt, `htdigest2john` output | Medium–High | Very common target; check for dupes |
| 10 | Confirming password-policy weakness (hardening report) | any format, via `--test` c/s + a bounded incremental ETA | Low–Info (often informational) | Frame as work-factor evidence, not a "crack" |

The recurring pattern: the *bug* is the exposure (SQLi, IDOR to a backup, open bucket, `.git`/key leak). john's job is to answer the triager's inevitable question — "so what if the hashes leaked, aren't they hashed?" — with a concrete yes. john's particular edge over a GPU-only tool is rows 1 and 4–7: the native shadow context and the vast `*2john` disclosure surface. Rows 8 belong to authorized internal engagements far more than to public web bounties.

## Command recipes

Every recipe below is offline and sends nothing to the target, so the discipline is about *data handling and effort budget*, not rate limits. The low-cost settings are called out.

**Identify, then benchmark, then decide (always this order):**

```
john --list=formats | tr ',' '\n' | grep -i bcrypt   # confirm the exact format name
john --test --format=bcrypt                           # c/s on this hardware (many vs one salt)
john --show=types dump.hash                            # how john classifies each line
```

Outcome: fix the format name, learn the per-hash work factor, and confirm the input parses before committing time. If the "Many salts" rate times your wordlist size exceeds your budget on a slow hash, stop and write the work-factor finding instead of grinding.

**Unix shadow with full account context (john's home turf):**

```
umask 077
unshadow /etc/passwd /etc/shadow > hashes.txt
john --single hashes.txt                          # account-metadata pass first
john --wordlist=/usr/share/wordlists/rockyou.txt --rules hashes.txt
john --show --users=0 hashes.txt                  # focus the report on UID-0 accounts
```

Outcome: `unshadow` gives john usernames and GECOS so `--single` can try login-derived candidates; the `--show --users=0` filter keeps the report to privileged accounts instead of every recovered login.

**Extracted application hash (converter → crack → extract):**

```
ssh2john id_rsa > ssh.hash        # or zip2john secret.zip > zip.hash, 7z2john.pl ...
john --wordlist=/usr/share/wordlists/rockyou.txt ssh.hash
john --show ssh.hash
```

Outcome: the converter emits the crackable hash line; john autodetects it. Converters live in `/usr/lib/john/` (Perl as `*.pl`, Python as `*.py`) with wrappers in `/usr/bin/`. Extract the target file in its own empty directory first — it is untrusted input.

**Wordlist + rules, scoped pot, bounded runtime (the workhorse):**

```
john --wordlist=/usr/share/wordlists/rockyou.txt --rules=best64 \
  --pot=./engagement.pot --session=webapp --max-run-time=3600 webapp.hash
```

Outcome: rockyou mangled by the best64 ruleset against the hashes; `--pot=` isolates results to this engagement, `--session=webapp` names the run so `--restore=webapp` can resume it, and `--max-run-time=3600` is a self-imposed one-hour effort cap. Swap `best64` for `Jumbo`, `KoreLogic`, or `All` to widen coverage.

**Preview candidates without cracking (`--stdout`):**

```
john --wordlist=rockyou.txt --rules=All --stdout | wc -l
john --mask='?u?l?l?l?l?d?d?d?d' --stdout | head
```

Outcome: `--stdout` prints the exact candidate stream a stage would generate — no hash file, no cracking. Use it to size a run, sanity-check a custom rule or mask, or feed another checker. Pipe the rule output through the bundled `unique` to deduplicate (`... --stdout | unique mangled.lst`).

**Mask on a fast format; hybrid mask on a wordlist:**

```
john --format=nt ntlm.hash --mask='?u?l?l?l?l?l?d?d' --min-length=7
john --format=nt ntlm.hash --wordlist=rockyou.txt --mask='?w?d?d?d'
```

Outcome: the first models a known password shape directly; the second appends three digits to every wordlist word (`?w` = the parent word). Reserve masks and incremental for fast formats — on bcrypt/argon2 the keyspace is infeasible and the attempt is wasted.

**Train a charset from cracked passwords, then run targeted incremental:**

```
john --make-charset=custom.chr --pot=./engagement.pot
# add to john.conf:  [Incremental:Custom]\n File = custom.chr
john --incremental=Custom --format=nt --max-run-time=1800 ntlm.hash
```

Outcome: `--make-charset` builds a `.chr` from the frequency of characters in already-cracked passwords, so a follow-up `--incremental=Custom` run is biased toward the patterns this target actually uses — bounded, as always, with `--max-run-time`.

**Loopback — password reuse across the set:**

```
john --loopback --rules --pot=./engagement.pot webapp.hash
```

Outcome: already-cracked pot passwords become the wordlist (duplicates suppressed), with rules applied — the cheap way to catch users who reused or lightly mutated a password another account already gave up.

**Write a target-specific rule (inline or in john.conf):**

```
# inline: capitalize + append the two-digit years this target favours
john --wordlist=words.txt --rules=':c $2 $[0-9]' --stdout | head
# or a named section in john.conf, then select it:
#   [List.Rules:Target]
#   c $2$0$2$5
john --wordlist=words.txt --rules=Target webapp.hash
```

Outcome: john's rule engine takes commands like `l`/`u`/`c` (case), `r` (reverse), `d` (duplicate), `$X` (append), `^X` (prefix), `sXY` (substitute), `@X` (purge), with reject flags and a `[...]` preprocessor that expands one line into many (`$[0-9]` → ten rules). Inline rules follow `--rules=:` and are separated by `;`; a named `[List.Rules:NAME]` section is selected with `--rules=NAME`. Encoding the password *policy you observed* as a small ruleset beats a generic megaruleset on a slow hash.

**Dynamic compiler — a hash construction with no built-in mode:**

```
john --format='dynamic=md5(md5($p).$s)' --wordlist=rockyou.txt app.hash
```

Outcome: the `dynamic=` expression compiler lets you describe a custom nested/salted construction on the command line when an app rolled its own scheme — a capability hashcat has no direct equivalent for. `--list=subformats` shows the predefined `dynamic_N` subformats; see the build's `doc/DYNAMIC*` for the full expression grammar before relying on an exact form.

**External mode — scripted candidates or a filter (john-only):**

```
john --external=Filter_Alpha --incremental=Alpha hashes.txt
john --make-charset=my_alpha.chr --external=filter_alpha mypasswd
```

Outcome: `[List.External:NAME]` sections in john.conf hold C-subset functions john compiles at startup; `--external=NAME` uses one as a candidate generator or a `filter()` that drops candidates (here, keeping alpha-only). There is no hashcat analog — useful for bespoke policy constraints a mask cannot express.

**Parallelise across cores and manage long sessions:**

```
john --wordlist=rockyou.txt --rules --fork=4 --session=job --pot=./engagement.pot webapp.hash
john --status=job            # from another terminal while it runs
john --restore=job           # resume after [q]uit or a crash
```

Outcome: `--fork=4` splits the keyspace across four processes (Unix-like only; not combined with `--status`/`--restore` in the same command), `--node=1-2/4` splits across machines, and the named session makes the run resumable.

**Extract and manage results:**

```
john --show --pot=./engagement.pot --format=bcrypt webapp.hash
john --show=left --pot=./engagement.pot --format=bcrypt webapp.hash
```

Outcome: `--show` prints only the already-cracked `user:password` pairs (no cracking run); `--show=left` prints only the uncracked hashes. `--show=types` and `--show=invalid` help debug a mis-loaded list.

**Filter salts by cost (spend time on the cheap ones):**

```
john --costs=5:10 --wordlist=rockyou.txt --format=bcrypt bcrypt.hash
```

Outcome: loads only salts whose tunable cost falls in the range (here bcrypt work factors 5–10), so a dump mixing cheap and expensive salts cracks the affordable ones first.

## Pitfalls, noise and scope discipline

john sends no traffic, so the classic "you flooded us / you got banned" failure mode does not apply. The ways hunters get a john-backed report **rejected, or get themselves into legal and ToS trouble**, are all about scope and data handling:

- **Cracking beyond the finding.** The disclosure of a hash store is the bug; cracking one credential proves impact. Cracking the whole table does not raise severity — it just means you now hold thousands of real people's passwords, and many programs treat mass-cracking of exfiltrated user data as a scope violation regardless of how you got the dump. Crack the minimum, document, delete.
- **Sending hashes to online/cloud crackers.** Web "hash lookup" sites and GPU-rental portals mean uploading *the program's data to a third party* — an uncontrolled disclosure, frequently a hard rules-of-engagement breach and a privacy problem, even when it is faster. Crack locally; never attach client identity to anything leaving your machine.
- **Using the plaintext to log in.** A crackable hash and an authenticated session are two different actions. Unless the program explicitly permits credential validation, stop at "the hash cracks to `<redacted>`"; prefer a *test account you control* when you must prove it.
- **Pot-file bleed.** john's default `$JOHN/john.pot` persists every crack across all runs and clients, and john silently skips any hash already in the pot. Without `--pot=`, a bare `--show` can surface another engagement's credentials into this report, or a stale crack can make you think you solved a hash you didn't — and you cannot cleanly *re-test* a hash the pot already holds. Use a per-engagement pot and delete it on closure.
- **Wrong format = wrong conclusion.** Autodetect can mis-load (raw-MD5 vs NT, a truncated converter line). Guessing the format, or trusting autodetect for the report, can make a strong hash look uncrackable or a weak one look strong. Pin `--format`, sanity-check with `--show=types` / `--show=invalid`, and record the john version (jumbo vs. core changes what is even supported).
- **Converter quirks.** `*2john` output *is* the hash — keep the original file out of the crack directory. Some converters have version sensitivities (new OpenSSH key formats, Office variants); confirm the line actually loads (`--show=invalid` flags lines that failed `valid()`) before concluding "it won't crack."
- **Single-crack mis-attribution.** `--single` tries each account's metadata candidates only against that account (and same-salt hashes). "It didn't fall in single mode" for one user does not mean another user's password isn't username-derived — do not over-generalise from a single-mode miss.
- **Leaving incremental running unbounded.** Incremental mode does not terminate; walking away from it and later calling the hash "uncrackable" is dishonest. Bound every incremental run and report the bound you used.
- **Reading a benchmark as the target's weakness.** descrypt or NT cracking at millions of c/s is a statement about the *algorithm*, not the user. Keep the benchmark in the algorithm-choice part of the report, separate from any claim about password strength.
- **Keeping the data.** Hashes and plaintexts are sensitive PII. Store them encrypted at rest for the life of the report only, and securely delete every artifact — hash files, converter output, `john.pot`, `john.log`, `.rec` session files, any `--stdout` dumps — when the report is resolved. Say in the report that you did so.

## Reporting evidence

A john-backed finding needs enough for a triager to reproduce the classification and believe the impact — without you shipping them a pile of live credentials.

- **The source of the hash.** Which in-scope vulnerability produced it (request/response or steps), proving the crack is downstream of an authorized finding — this is what makes the whole thing in scope.
- **A redacted sample hash**, not the whole dump. One line, account identifier masked, showing the structure that justifies the format.
- **Format identification:** the `--list=formats` / `--show=types` match and the final `--format=` name, plus the **exact john version** (startup banner or `--list=build-info`) — jumbo vs. core and the build date decide reproducibility.
- **The benchmark:** `john --test --format=<name>` output, including both the "Many salts" and "Only one salt" c/s figures — the core of the algorithm-strength argument.
- **The exact attack command**, including wordlist, ruleset, and any mask, so the effort is reproducible — and the **time-to-crack** from the status line.
- **Proof of the crack, redacted.** The `--show` line with the plaintext masked (`user:Pa****rd1`) or its length/character-class only, unless the program explicitly wants the value.
- **The honest severity framing.** State plainly which finding you are making: *algorithm weakness* ("passwords are stored as descrypt/unsalted MD5; any password cracks in seconds — see benchmark") versus *password weakness* ("despite bcrypt cost 12, this credential fell to a 14M-word dictionary in 9 minutes"). The remediation differs (migrate the hashing scheme vs. enforce password policy / breached-password checks), so the distinction belongs in the report.
- **Data-handling note.** State that cracking was local, that no data went to third-party services, that you cracked the minimum needed to prove impact, and that all hash/plaintext material will be securely deleted on resolution.
- **Scope note for credentials.** If the program forbade login with recovered credentials, say explicitly that you did not authenticate — the report stands on the disclosure plus the crack, not on account takeover.

### Coverage closeout — know when you are done

Frame coverage as claims you tried to *disprove*, and state the bound you did not cross so any residual-risk note stays honest:

| Claim | Backed by | Honest limit to record |
|---|---|---|
| "This credential is crackable" | the `--show` line + time-to-crack | the one credential; you did not crack the rest |
| "The algorithm is the weakness" | `--test` c/s for the format | holds for *any* password under that format |
| "The password is the weakness" | crack despite a high work factor (`--test`, `--costs`) | one user; others may be strong |
| "Exhaustive search is infeasible" | `--test` × keyspace ETA | only for the charset/length bound you set — incremental never *finishes* |
| "The input really is format X" | `--show=types` / `--show=invalid` clean load | pinned `--format` + john version |

The claim you can rarely close is "no password in this set is crackable" — incremental does not terminate, so report the time/keyspace bound you actually ran, never an unbounded "uncrackable."

## References

- Openwall — John the Ripper homepage and documentation index: https://www.openwall.com/john/
- john doc: OPTIONS (bleeding-jumbo, authoritative for jumbo flags): https://github.com/openwall/john/blob/bleeding-jumbo/doc/OPTIONS
- john doc: MODES (single/wordlist/incremental/external, default cascade): https://www.openwall.com/john/doc/MODES.shtml
- john doc: RULES (wordlist mangling rules and sections): https://www.openwall.com/john/doc/RULES.shtml
- john doc: EXAMPLES (canonical command lines): https://www.openwall.com/john/doc/EXAMPLES.shtml
- john doc: MASK (mask-mode placeholders and hybrid `?w` stacking): https://raw.githubusercontent.com/openwall/john/bleeding-jumbo/doc/MASK
- Openwall wiki — wordlist-mode rulesets (KoreLogic and others): https://openwall.info/wiki/john/rules
- Openwall wiki — generating test hashes per format: https://openwall.info/wiki/john/Generating-test-hashes
- john GitHub repository (bleeding-jumbo, `run/` converters): https://github.com/openwall/john
- Arch Linux package file list (bundled `*2john` converters and helper binaries): https://archlinux.org/packages/extra/x86_64/john/files/
- Companion: [[hashcat]] — the GPU cracker and its bug-bounty supplement; run both.

*Research supplement added 2026-11-15, grounded in the Openwall John the Ripper docs (OPTIONS/MODES/RULES/EXAMPLES/MASK), the openwall.info rules wiki, and the openwall/john bleeding-jumbo tree — flags, format names, and bundled converters verified against those sources and the Arch 1.9.0.jumbo1 package file list (model: claude-opus-4-8).*
