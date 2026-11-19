---
name: "nimrm"
categories:
  - blackarch-windows
version: "v1.2.0.r12.g322c670"
homepage: "https://github.com/blue0x1/nimrm"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-13"
build-system: windows
license: MIT
meta-checked: "2026-11-09"
github-repo: "blue0x1/nimrm"
github-stars: 6
github-pushed: "2026-09-11"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 44
gh-activity-checked: "2026-11-09"
---

# nimrm

> Native Nim WinRM shell client with NTLM, pass-the-hash, Kerberos, TLS, file transfer and in-memory PowerShell support.

## What it's for

nimrm gives you an interactive remote shell on Windows machines whose WinRM endpoint (5985/5986) is reachable. It authenticates with an NTLM password or NT hash (pass-the-hash), or with Kerberos via a ccache ticket, and supports HTTP, HTTPS/TLS and custom ports. Beyond running commands, the shell uploads and downloads files, imports PowerShell scripts and executes .NET assemblies in memory, and can juggle several sessions at once. It is a client only — it does not enumerate a domain or exploit anything; reach for it once you already have credentials.

## Typical use cases

### Open an interactive shell with a password

Connect to a WinRM endpoint with plaintext credentials.

```
nimrm -T 198.51.100.10 -A 'EXAMPLE\user' -P 'Password1'
```

Outcome: opens an interactive PowerShell session on the target; type `/help` for the built-in commands, `exit` to close.

### Authenticate with pass-the-hash

Reuse an NT hash captured elsewhere, no plaintext needed.

```
nimrm -T 198.51.100.10 -A 'EXAMPLE\user' -N aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0
```

Outcome: authenticates over NTLM with the supplied `LM:NT` hash (`-N` also accepts a bare NT hash) and drops into the same interactive shell.

### Authenticate with Kerberos

Use an existing ticket cache instead of a password.

```
KRB5CCNAME=FILE:/tmp/user.ccache nimrm -k -T dc01.example.com -Z EXAMPLE.COM
```

Outcome: authenticates via Kerberos from the ccache against the realm EXAMPLE.COM and opens the shell; `-Z` overrides the realm when it differs from the target's domain.

### Run a single command and exit

Use it as a one-shot remote executor in scripts.

```
nimrm -T 198.51.100.10 -A 'EXAMPLE\user' -P 'Password1' -c 'whoami'
```

Outcome: runs `whoami` on the target, prints the output and exits — no interactive session is opened.

### Connect to HTTPS WinRM with a self-signed certificate

Handle lab endpoints whose TLS certificate does not validate.

```
nimrm -T 198.51.100.10 -A 'EXAMPLE\user' -P 'Password1' --tls --insecure -p 5986
```

Outcome: connects over HTTPS on port 5986, skipping certificate verification (`--insecure`), with the same interactive shell behind it.

*No corpus evidence — derived from upstream documentation (blue0x1/nimrm README).*
