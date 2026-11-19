---
name: "gtalk-decode"
categories:
  - blackarch-windows
version: "0.1"
homepage: "http://packetstormsecurity.com/files/119154/Google-Talk-Decoder.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# gtalk-decode

> Demonstrates recovering saved Google Talk login passwords from a Windows user profile.

## What it's for

Google Talk Decoder v1.0 (Kevin Devine, 2012; redistributed by
insecurety.net and Packet Storm) is a single-file demonstration of how the
Google Talk client stored login credentials: it enumerates the accounts
recorded under `HKCU\Software\Google\Google Talk\Accounts` in the current
user's registry hive and decrypts the stored password values using the
account's entropy material, printing the plaintext credentials. Today it
is of forensic and historical interest — a minimal compiled proof of the
legacy local-storage weakness — and a working example for examining
old profiles seized during incident response or account-recovery work. It
takes no arguments and only reads the interactive user's own profile; it
cannot attack other users or remote hosts.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Dump saved Google Talk credentials for the current user

Recover the passwords stored by a legacy Google Talk installation.

```
gtalk_decode.exe
```

Outcome: enumerates the Google Talk accounts under the current user's
registry profile, decrypts each saved password and prints account/
password pairs to the console (then waits for a keypress before exiting).
