---
name: "goshs"
categories:
  - blackarch-misc
version: "2.1.0"
homepage: "https://github.com/goshs-labs/goshs"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-05-30"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "goshs-labs/goshs"
github-stars: 1008
github-pushed: "2026-11-05"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 473
gh-activity-checked: "2026-11-09"
---

# goshs

> Single-binary file server with HTTP/S, WebDAV, FTP/SFTP, SMB and LDAP listeners plus credential-capture features for engagements.

## What it's for

goshs is an extended replacement for `python3 -m http.server`: it serves directories over HTTP/S and WebDAV with upload, basic and certificate auth, share links, and read-only/upload-only modes — and it doubles as a capture host with SMB NTLM hash capture, LDAP credential capture (including a JNDI mode for Log4Shell-style callbacks), DNS and SMTP listeners, a reverse-shell catcher, and a terminal dashboard. Reach for it when a transfer or payload-delivery task needs more than a plain file server but you don't want to configure a full web stack. It does not proxy or route arbitrary traffic — it serves files and catches the callbacks it advertises.

## Typical use cases

### Serve the current directory over HTTP

Stand up a quick file-exchange page with upload support on the default port 8000.

```
goshs
```

Outcome: a web file browser for the working directory is served on port 8000 with download and upload support.

### Serve over HTTPS with basic auth

Wrap the file server in a self-signed TLS certificate and require credentials.

```
goshs -s -ss -b user:password
```

Outcome: the file browser is served over HTTPS with a self-signed certificate and requires user:password to access.

### Capture SMB NTLM hashes

Run the SMB listener that responds to Windows authentication attempts so that clients directed at it leak Net-NTLM hashes.

```
goshs -smb -smb-domain CORP
```

Outcome: any SMB authentication attempt against the host is answered and the captured Net-NTLM hashes appear in the goshs interface, ready for offline cracking or relay.

### Capture LDAP credentials and crack them

Answer LDAP binds (for example from a crafted JNDI lookup URL) and optionally crack captured hashes against a wordlist on the fly.

```
goshs -ldap -ldap-wordlist /usr/share/wordlists/rockyou.txt
```

Outcome: LDAP authentication attempts against the listener are logged with captured NTLM hashes, which are cracked against the wordlist and reported when successful.

### Serve templated payloads with callback values

Render `{{.LHOST}}`/`{{.LPORT}}` placeholders and custom variables into served files at download time so a single payload file fits the current listener.

```
goshs -i 198.51.100.10 --template --tpl-var LPORT=4444
```

Outcome: fetching a served file with `?tpl` appended (for example `http://198.51.100.10:8000/rev.ps1?tpl`) returns it with the template variables substituted.

### Run with the terminal dashboard

Start goshs with its full-screen interactive TUI instead of web-only output, useful over headless SSH sessions.

```
goshs --tui
```

Outcome: an interactive terminal dashboard opens showing live HTTP, DNS, SMB, LDAP and reverse-shell activity, with shell attach/upgrade controls.

### Lock the share to one direction

Serve downloads only, or collect uploads only.

```
goshs -ro
goshs -uo
```

Outcome: `-ro` (read-only) removes upload support — safe payload hosting visitors cannot write to; `-uo` (upload-only) removes downloads, turning the server into a pure drop box for collecting files.

### Catch DNS callbacks

Answer the DNS queries implants and exfil payloads make.

```
goshs -dns -dns-ip ATTACKER_IP
```

Outcome: starts a DNS server (default port 8053) alongside the web UI; received queries show up live in the interface, and `-dns-ip` sets the address returned in the replies — the receiving end for tools that beacon or exfiltrate over DNS.

### Receive mail callbacks

Accept SMTP deliveries for a chosen domain.

```
goshs -smtp -smtp-domain example.com
```

Outcome: starts an SMTP server (default port 2525) that accepts mail addressed to the given domain and surfaces it in the dashboard — the catch side for payloads that report out by sending mail.

### Tunnel the server to a public URL

Expose the local instance without touching firewall rules.

```
goshs --tunnel
```

Outcome: forwards the server through localhost.run and prints a public HTTPS URL mapping to the local instance — the way to serve a payload when the target can reach the internet but not your internal address.

*Command patterns sourced from HackTricks pages (CC BY-NC reference; rewritten); flags verified against the official goshs documentation.*
