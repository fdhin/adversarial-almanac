---
name: "ghostpop3"
categories:
  - honeypot
version: "0.1.0"
homepage: "http://kernel.embedromix.ro/us/"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ghostpop3

> Minimal POP3 decoy whose mailbox is always empty but which logs every username and password clients submit.

## What it's for

ghostpop3 (Catalin(ux aka Dino) Boie, 2006, C) simulates a POP3 server that accepts logins and then always reports zero messages. Its documented purposes: harvesting the user/password pairs clients actually send — for example to verify credentials in use before or during a mail migration — and standing in for a broken POP3 server so clients silently see "no new mail" instead of calling support while the real server is repaired. It listens on IPv4 and IPv6, accepts thousands of concurrent connections, and is configured entirely through `ghostpop3.conf` (port, daemonization, log path, privilege dropping, `log_user_pass`). It never stores or delivers mail — it is a credential-capturing stub only.

## Typical use cases

### Deploy the empty-mailbox decoy

Run the fake POP3 service with credential logging turned on.

```
ghostpop3
```

Outcome: the binary reads the `ghostpop3.conf` at its compiled-in configuration path (default settings: port 9110, `daemon = on`, drop to nobody/nogroup), daemonizes, and accepts POP3 logins on IPv4 and IPv6; with `log_user_pass = on` every USER/PASS pair is appended to the configured log file (default `/var/log/ghostpop3.log`) while the client is told the mailbox is empty.

### Verify the decoy behaves like a real POP3 server

Probe your own listener to confirm what clients will experience.

```
curl --user probe:Password1 pop3://198.51.100.10:9110
```

Outcome: the session authenticates and the LIST exchange returns no messages; the probe credentials now appear as a captured user/pass pair in ghostpop3's log file, confirming both the silent-mailbox behaviour and the credential capture path.

*No corpus evidence — derived from upstream documentation.*
