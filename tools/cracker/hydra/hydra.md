---
name: "hydra"
categories:
  - passwords
  - top10
  - web
version: "9.7-1"
homepage: "https://github.com/vanhauser-thc/thc-hydra"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-05-15"
build-system: autotools
license: AGPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "vanhauser-thc/thc-hydra"
github-stars: 12474
github-pushed: "2026-10-30"
github-archived: false
github-issues: 52
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 89
gh-activity-checked: "2026-11-09"
---

# hydra

> Parallelized network logon cracker for 50+ protocols

## What it's for

THC Hydra performs online password guessing against live network services — SSH, FTP, HTTP/HTTPS forms and basic auth, MySQL/MSSQL/PostgreSQL, LDAP, RDP, VNC, SMB, SMTP, SNMP, Telnet and dozens more — using many parallel connections. Credentials come from single values, wordlist files, colon-separated `login:password` combo files, or generated brute-force charsets; for web forms you teach it the failure string so it can judge each attempt. It is the default first try when a reachable login prompt is the way in: quick to point at a target, tunable with thread counts and port options, resumable after a crash, and scriptable through JSON output.

## Typical use cases

### Password-list attack on SSH

```
hydra -l user -P passwords.txt ssh://target.example.com
```

Outcome: tries every password in passwords.txt for the single login `user` over SSH and prints a `[ssh] host: login: user   password: <found>` line for each valid pair; swap `-L users.txt` for `-l` to iterate a username list, and `-e nsr` additionally tries the login as password, an empty password and the reversed login.

### Web login form brute force

```
hydra -l user -P passwords.txt 198.51.100.10 http-post-form "/login.php:username=^USER^&password=^PASS^:F=Login failed"
```

Outcome: submits the form for each candidate with `^USER^`/`^PASS^` substituted; any response containing the `F=` string counts as failed and everything else as a hit, which is reported like `[http-post-form] host: login: user   password: <found>`. Use `https-post-form` for SSL endpoints, `S=` to match on success instead of failure, and `hydra -U http-post-form` for the module's full option list (cookies `H=`, etc.).

### Spray a combo list against one form

```
hydra -C userpass.txt target.example.com http-post-form "/index.php:username=^USER^&password=^PASS^:F=invalid"
```

Outcome: reads `login:password` pairs one per line from userpass.txt and fires only those pairs — the efficient shape when you already hold a leaked or default-credential list; in `-C` mode the individual `-l/-L/-p/-P` options are not allowed.

### Try cheap credential guesses first

```
hydra -l user -e nsr -p Password1 ssh://target.example.com
```

Outcome: before any dictionary run, covers the classic weak-password cases in seconds — the login as its own password (`s`), an empty password (`n`) and the reversed login (`r`) — on top of the single literal password given.

### Tuned scan with saved results

```
hydra -L users.txt -P passwords.txt -t 16 -u -o found.txt -b jsonv1 ftp://target.example.com
```

Outcome: runs 16 parallel tasks, tries all passwords for one login before moving to the next (`-u` surfaces weak-password hits sooner), and writes every finding to found.txt in JSON (via `-b jsonv1`, alongside plain text) for downstream tooling; an aborted or crashed run leaves a hydra.restore file holding the session state for resuming.

### Brute-force HTTP basic authentication

```
hydra -L users.txt -P passwords.txt 198.51.100.10 http-get /manager/html
```

Outcome: runs each pair against the URI's basic-auth prompt and reports `[http-get] host: login: user password: <found>`; the path is the module option (equivalently written `http-get://198.51.100.10/manager/html`), `https-get` does the same over TLS, and `-s PORT` reaches it on a non-default port. The classic target is a Tomcat manager behind `/manager/html`.

### Attack services that have only one credential field

```
hydra -P community-strings.txt -s 161 198.51.100.10 snmp
```

Outcome: SNMP has no username, so only `-P` is given — every wordlist entry is tried as the community string, and the same single-field shape fits Redis. The mirror image is username-only brute force: `hydra -L users.txt rsh://198.51.100.10` needs no password list because rsh/rlogin authenticate on hostname and account alone.

### Reach SSL-wrapped services on non-default ports

```
hydra -l user -P passwords.txt -s 993 -S -f 198.51.100.10 imap
```

Outcome: `-s 993` overrides the module's default port and `-S` makes hydra negotiate SSL from the first byte — required against implicit-TLS services such as IMAPS on 993 or POP3S on 995, which close plaintext connections immediately; `-f` halts the whole run at the first valid login. Without `-S` the same command fails against every implicit-TLS port.
