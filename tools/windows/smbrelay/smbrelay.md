---
name: "smbrelay"
categories:
  - blackarch-windows
version: "3"
homepage: "http://www.tarasco.org/security/smbrelay/"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-13"
build-system: windows
license: GPL
meta-checked: "2026-11-09"
github-repo: "Seabreg/smbrelay3"
github-stars: 3
github-pushed: "2019-03-24"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# smbrelay

> SMBRelay3 — NTLM replay toolkit that captures an HTTP/SMB/mail-client authentication and relays it into SMB, returning a shell when the credential is privileged.

GitHub: third-party mirror of the tarasco.org SMBRelay3 distribution.

## What it's for

SMBRelay3 (Andres Tarasco, tarasco.org, 2007–2008) exploits the fact that NTLM authentication is not session-bound: a captured authentication exchange can be replayed against a third-party host. The tool listens for a victim client and completes an NTLM negotiation with it — over HTTP or SMB, and later over IMAP, POP3 and SMTP as well, targeting mail clients that send credentials — while relaying the same authentication into an SMB session against a chosen target. It implements its own SMB library for crafting raw messages, and when the relayed account has administrative privileges on the target it automatically uploads a helper binary to `admin$`, registers it as a service and returns an interactive remote shell. A psexec-style module gives the same shell when the username and password are already known. Getting victims to connect (DNS poisoning, email/IM links, XSS) is the operator's positioning problem; networks relying on NTLM without SMB signing remain the vulnerable configuration. This is the lineage that later tools such as Responder and impacket's relay tooling industrialized.

## Typical use cases

### Relay a browser's NTLM authentication into SMB

```
smbrelay3.exe --ListForHTTPRequests --AlternativeHTTPPort 81
```

Outcome: starts an HTTP listener on port 81; a victim browser steered to it receives a 401 with an NTLM challenge, and the captured authentication is replayed against the configured SMB target (e.g. 198.51.100.10:445) — the console logs each step (handshake, NTLM packet, username/domain) and, on success with an administrative account, uploads `smrs.exe` to `admin$`, opens and starts a service and yields a remote shell.

*No corpus evidence — derived from upstream documentation.*
