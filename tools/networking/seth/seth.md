---
name: "seth"
categories:
  - blackarch-networking
version: "106.6e869cd"
homepage: "https://github.com/SySS-Research/Seth"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "SySS-Research/Seth"
github-stars: 1457
github-pushed: "2025-11-20"
github-archived: false
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# seth

> RDP man-in-the-middle that downgrades NLA to harvest clear-text credentials.

## What it's for

Seth (Adrian Vollmer, SySS GmbH) is a Python/Bash tool for MitM attacks against RDP: it ARP-spoofs its way between a victim and an RDP host, clones the server's x509 certificate, downgrades the authentication (CredSSP/NLA to weaker protocol choices) and intercepts the login — yielding both an offline-crackable NTLM response and, when the downgrade succeeds, the clear-text `DOMAIN\user:password`. It was written to demonstrate the impact of misconfigured RDP (NLA not enforced, certificate warnings ignored) in pentests and awareness training. The wrapper depends on `arpspoof` (dsniff), `tcpdump` and `openssl`; the RDP proxy (`seth.py`) can also be driven standalone behind your own MITM tooling such as Responder.

## Typical use cases

### Full attack with one command

Let the wrapper handle ARP spoofing, redirection and credential capture.

```
sudo seth.sh eth0 ATTACKER_IP VICTIM_IP GATEWAY_IP
```

Outcome: spoofs ARP replies, enables IP forwarding, waits for the victim's RDP SYN, clones the destination certificate, downgrades the offered authentication options (e.g. 11 to 3), prints the intercepted NTLM response and then the clear-text credentials (`.\user:password`); unless the RDP host is on the victim's subnet, the last address must be the gateway.

### Run only the RDP proxy behind your own MITM

Reuse an existing MITM position instead of self-ARP-spoofing.

```
seth.py -c cert.pem -k key.pem 198.51.100.10
```

Outcome: runs just the credential-sniffing RDP proxy against host 198.51.100.10 (default port 3389; `-p`/`-b` change listen port and bind address) with `-g {0,1,3,11}` choosing the protocol level to downgrade the client to — combine with Responder holding the MITM position.

### Execute a command on the RDP host

Turn a successful capture into command execution via keystroke injection.

```
sudo seth.sh eth0 ATTACKER_IP VICTIM_IP GATEWAY_IP 'powershell -enc <BASE64>'
```

Outcome: after the downgrade, simulates WIN+R and types the command on the host via key-press injection; reliable only for the English-US keyboard layout — prefer UTF-16LE+Base64-encoded payloads (`powershell -enc`), or plain `calc` as the layout-independent demo.

*No corpus evidence — derived from upstream documentation.*
